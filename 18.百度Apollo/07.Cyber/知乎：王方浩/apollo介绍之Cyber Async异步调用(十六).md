- [apollo介绍之Cyber Async异步调用(十六) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/121751141)

下面介绍下cyber的异步调用接口"cyber::Async"，启动异步执行任务。

## 异步调用

在"task.h"中定义了异步调用的方法包括**"Async","Yield","SleepFor","USleep"**异步调用方法，用户可以通过上述方法实现异步调用。下面我们逐个看下上述方法的实现。



**Async方法**

```cpp
template <typename F, typename... Args>
static auto Async(F&& f, Args&&... args)
    -> std::future<typename std::result_of<F(Args...)>::type> {
  return GlobalData::Instance()->IsRealityMode()
             ? TaskManager::Instance()->Enqueue(std::forward<F>(f),
                                                std::forward<Args>(args)...)
             : std::async(
                   std::launch::async,
                   std::bind(std::forward<F>(f), std::forward<Args>(args)...));
}
```

如果为真实模式，则采用"TaskManager"的方法生成协程任务，如果是仿真模式则创建线程。



TaskManager实际上实现了一个任务池，最大执行的任务数为scheduler模块中设置的TaskPoolSize大小。超出的任务会放在大小为1000的有界队列中，如果超出1000，任务会被丢弃。下面我们看TaskManager的具体实现。

```cpp
TaskManager::TaskManager()
// 1. 设置有界队列，长度为1000
    : task_queue_size_(1000),
      task_queue_(new base::BoundedQueue<std::function<void()>>()) {
  if (!task_queue_->Init(task_queue_size_, new base::BlockWaitStrategy())) {
    AERROR << "Task queue init failed";
    throw std::runtime_error("Task queue init failed");
  }
  // 2. 协程任务，每次从队列中取任务执行，如果没有任务则让出协程，等待数据
  auto func = [this]() {
    while (!stop_) {
      std::function<void()> task;
      if (!task_queue_->Dequeue(&task)) {
        auto routine = croutine::CRoutine::GetCurrentRoutine();
        routine->HangUp();
        continue;
      }
      task();
    }
  };

  num_threads_ = scheduler::Instance()->TaskPoolSize();
  auto factory = croutine::CreateRoutineFactory(std::move(func));
  tasks_.reserve(num_threads_);
  // 3. 创建TaskPoolSize个任务并且放入调度器
  for (uint32_t i = 0; i < num_threads_; i++) {
    auto task_name = task_prefix + std::to_string(i);
    tasks_.push_back(common::GlobalData::RegisterTaskName(task_name));
    if (!scheduler::Instance()->CreateTask(factory, task_name)) {
      AERROR << "CreateTask failed:" << task_name;
    }
  }
}
```

\1. 协程承载运行具体的任务，也就是说如果任务队列中有任务，则调用协程去执行，如果队列中没有任务，则让出协程，并且设置协程为等待数据的状态，那么让出协程之后唤醒是谁去触发的呢？

答：每次在任务队列中添加任务的时候，会唤醒协程执行任务。

\2. task_queue_会被多个协程访问，并发数据访问这里没有加锁，需要看下这个队列是如何实现的？？？

\3. 上述的任务可以在"conf"文件中设置"/internal/task + index"的优先级来实现。



接着看下Enqueue的实现，加入任务到任务队列。

```cpp
  template <typename F, typename... Args>
  auto Enqueue(F&& func, Args&&... args)
    // 1. 返回值为future类型
      -> std::future<typename std::result_of<F(Args...)>::type> {
    using return_type = typename std::result_of<F(Args...)>::type;
    auto task = std::make_shared<std::packaged_task<return_type()>>(
        std::bind(std::forward<F>(func), std::forward<Args>(args)...));
    if (!stop_.load()) {
      // 2. 将函数加入任务队列，注意这里的任务不是调度单元里的任务，可以理解为一个函数
      task_queue_->Enqueue([task]() { (*task)(); });
      // 3. 这里的任务是调度任务，唤醒每个协程执行任务。
      for (auto& task : tasks_) {
        scheduler::Instance()->NotifyTask(task);
      }
    }
    std::future<return_type> res(task->get_future());
    return res;
  }
```

每次在任务队列中添加任务的时候，唤醒任务协程中所有的协程。



**Yield和Sleep方法**

Yield方法和Async类似，如果为协程则让出当前协程，如果为线程则让出线程。SleepFor和USleep方法类似，这里就不展开了。

```cpp
static inline void Yield() {
  if (croutine::CRoutine::GetCurrentRoutine()) {
    croutine::CRoutine::Yield();
  } else {
    std::this_thread::yield();
  }
}
```



这里顺便也介绍下SysMo系统监控

## SysMo系统监控

SysMo模块的用途主要是监控系统协程的运行状况。

**Start启动**

在start中单独启动一个线程去进行系统监控，这里没有设置线程的优先级，因此不能在"conf"文件中设置优先级？？？

```cpp
void SysMo::Start() {
  auto sysmo_start = GetEnv("sysmo_start");
  if (sysmo_start != "" && std::stoi(sysmo_start)) {
    start_ = true;
    sysmo_ = std::thread(&SysMo::Checker, this);
  }
}
```



**Checker**

每隔100ms调用一次Checker，获取调度信息。

```cpp
void SysMo::Checker() {
  while (cyber_unlikely(!shut_down_.load())) {
    scheduler::Instance()->CheckSchedStatus();
    std::unique_lock<std::mutex> lk(lk_);
    cv_.wait_for(lk, std::chrono::milliseconds(sysmo_interval_ms_));
  }
}
```



打印的是协程的调度快照。

```cpp
void Scheduler::CheckSchedStatus() {
  std::string snap_info;
  auto now = Time::Now().ToNanosecond();
  for (auto processor : processors_) {
    auto snap = processor->ProcSnapshot();
    if (snap->execute_start_time.load()) {
      auto execute_time = (now - snap->execute_start_time.load()) / 1000000;
      snap_info.append(std::to_string(snap->processor_id.load()))
          .append(":")
          .append(snap->routine_name)
          .append(":")
          .append(std::to_string(execute_time));
    } else {
      snap_info.append(std::to_string(snap->processor_id.load()))
          .append(":idle");
    }
    snap_info.append(", ");
  }
  snap_info.append("timestamp: ").append(std::to_string(now));
  AINFO << snap_info;
  snap_info.clear();
}
```