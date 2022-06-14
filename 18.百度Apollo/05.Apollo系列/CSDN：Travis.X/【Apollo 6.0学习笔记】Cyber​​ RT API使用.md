- [【Apollo 6.0学习笔记】Cyber RT API使用_Travis.X的博客-CSDN博客](https://blog.csdn.net/Travis_X/article/details/120987330)

# 前言

本文档对如何创建、操作和使用 Cyber RT 的 [API](https://so.csdn.net/so/search?q=API&spm=1001.2101.3001.7020) 进行了广泛的技术深入探讨。

------

# 一、Talker-Listener

以Talker/Listener 为例，实例设计了三个基本的概念Node（基本单元）、Reader（读取消息的功能）、Writer（写入消息的功能）。

## 1. create a node（创建节点）

在CyberRT框架中，节点是最基本的单元，类似于的角色handle。创建特定的功能对象（编写器，读取器等）时，需要基于现有节点实例来创建它。

节点创建界面如下：

```clike
std::unique_ptr<Node> apollo::cyber::CreateNode(const std::string& node_name, const std::string& name_space = "");
```

参数：【Apollo 6.0项目实战】Apollo 6.0安装

- node_name：节点名称，全局唯一标识符
- name_space：节点所在空间的名称
- name_space默认为空。它是与node_name串联的空间的名称。格式为/namespace/node_name

返回值：

- 指向Node的专有智能指针。

错误条件：

- cyber::Init()未调用时，系统处于未初始化状态，无法创建节点，返回nullptr

------

## 2.Create a writer（创建编写器）

编写器是CyberRT中用于发送消息的基本工具。每个writer对应于具有特定数据类型的通道。编写器由CreateWriter节点类中的接口创建。

```clike
template <typename MessageT>
   auto CreateWriter(const std::string& channel_name)
       -> std::shared_ptr<Writer<MessageT>>;
template <typename MessageT>
   auto CreateWriter(const proto::RoleAttributes& role_attr)
       -> std::shared_ptr<Writer<MessageT>>;
```

参数：

- channel_name：要写入的通道的名称
- MessageT：要写出的消息类型

返回值：

- 指向Writer对象的共享指针

------

## 3. Create a reader（创建读取器）

这个reader是网络中用于接收消息的基本工具。创建reader时，必须将其绑定到回调函数。当新消息到达频道时，将调用回调。reader是由CreateReader节点类的接口创建的。

接口列表如下：

```clike
template <typename MessageT>
auto CreateReader(const std::string& channel_name, const std::function<void(const std::shared_ptr<MessageT>&)>& reader_func)
    -> std::shared_ptr<Reader<MessageT>>;

template <typename MessageT>
auto CreateReader(const ReaderConfig& config,
                  const CallbackFunc<MessageT>& reader_func = nullptr)
    -> std::shared_ptr<cyber::Reader<MessageT>>;

template <typename MessageT>
auto CreateReader(const proto::RoleAttributes& role_attr,
                  const CallbackFunc<MessageT>& reader_func = nullptr)
-> std::shared_ptr<cyber::Reader<MessageT>>;
```

参数：

- MessageT：要阅读的消息类型
- channel_name：要从中接收的频道的名称
- reader_func：处理消息的回调函数

返回值：

- 指向Reader对象的共享指针

------

## 4. 代码示例

Talker（cyber / examples / talker.cc）

```clike
#include "cyber/cyber.h"
#include "cyber/proto/chatter.pb.h"
#include "cyber/time/rate.h"
#include "cyber/time/time.h"
using apollo::cyber::Rate;
using apollo::cyber::Time;
using apollo::cyber::proto::Chatter;
int main(int argc, char *argv[]) {
  // init cyber framework
  apollo::cyber::Init(argv[0]);
  // create talker node
  std::shared_ptr<apollo::cyber::Node> talker_node(
      apollo::cyber::CreateNode("talker"));
  // create talker
  auto talker = talker_node->CreateWriter<Chatter>("channel/chatter");
  Rate rate(1.0);
  while (apollo::cyber::OK()) {
    static uint64_t seq = 0;
    auto msg = std::make_shared<apollo::cyber::proto::Chatter>();
    msg->set_timestamp(Time::Now().ToNanosecond());
    msg->set_lidar_timestamp(Time::Now().ToNanosecond());
    msg->set_seq(seq++);
    msg->set_content("Hello, apollo!");
    talker->Write(msg);
    AINFO << "talker sent a message!";
    rate.Sleep();
  }
  return 0;
}
```

Listener（cyber / examples / listener.cc）

```clike
#include "cyber/cyber.h"
#include "cyber/proto/chatter.pb.h"
void MessageCallback(
    const std::shared_ptr<apollo::cyber::proto::Chatter>& msg) {
  AINFO << "Received message seq-> " << msg->seq();
  AINFO << "msgcontent->" << msg->content();
}
int main(int argc, char *argv[]) {
  // init cyber framework
  apollo::cyber::Init(argv[0]);
  // create listener node
  auto listener_node = apollo::cyber::CreateNode("listener");
  // create listener
  auto listener =
      listener_node->CreateReader<apollo::cyber::proto::Chatter>(
          "channel/chatter", MessageCallback);
  apollo::cyber::WaitForShutdown();
  return 0;
}
```

Bazel BUILD file(cyber/samples/BUILD)

```clike
cc_binary(
    name = "talker",
    srcs = [ "talker.cc", ],
    deps = [
        "//cyber",
        "//cyber/examples/proto:examples_cc_proto",
    ],
)

cc_binary(
    name = "listener",
    srcs = [ "listener.cc", ],
    deps = [
        "//cyber",
        "//cyber/examples/proto:examples_cc_proto",
    ],
)
```

- 构建和运行

```clike
bazel build cyber/examples/..
```

- 在不同的终端中运行Talker/LIstener：

```clike
export GLOG_alsologtostderr=1
./bazel-bin/cyber/examples/talker
  
  export GLOG_alsologtostderr=1 
./bazel-bin/cyber/examples/listener
```

- 终端打印结果
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/87e7943fc6954d50bce25d7480802ba0.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 二、Service Creation and Use

在自动驾驶系统中，除了模块发送或接收消息外，还有很多场景需要模块通信。服务是节点之间的另一种通信方式。与信道不同，服务实现two-way通信，例如，节点通过发送请求获得响应。本节通过示例介绍CyberRT API 模块中的service。

## Demo - Example (演示-示例)

问题：创建来回传递Driver.proto的客户端-服务器模型。当客户端发送请求时，服务器解析/处理该请求并返回响应。

该Demo演示的实现主要包括以下步骤。

## 1. Define request and response messages (定义请求和响应消息)

cyber中的所有消息均采用protobuf格式。任何带有序列化/反序列化接口的protobuf消息都可以用作服务请求和响应消息。Driver在examples.proto中用作本示例中的服务请求和响应：

```cpp
// filename: examples.proto
syntax = "proto2";
package apollo.cyber.examples.proto;
message Driver {
    optional string content = 1;
    optional uint64 msg_id = 2;
    optional uint64 timestamp = 3;
};
```

## 2. Create a service and a client (创建服务和客户端)

```cpp
// filename: cyber/examples/service.cc
#include "cyber/cyber.h"
#include "cyber/examples/proto/examples.pb.h"

using apollo::cyber::examples::proto::Driver;

int main(int argc, char* argv[]) {
  apollo::cyber::Init(argv[0]);
  std::shared_ptr<apollo::cyber::Node> node(
      apollo::cyber::CreateNode("start_node"));
  auto server = node->CreateService<Driver, Driver>(
      "test_server", [](const std::shared_ptr<Driver>& request,
                        std::shared_ptr<Driver>& response) {
        AINFO << "server: I am driver server";
        static uint64_t id = 0;
        ++id;
        response->set_msg_id(id);
        response->set_timestamp(0);
      });
  auto client = node->CreateClient<Driver, Driver>("test_server");
  auto driver_msg = std::make_shared<Driver>();
  driver_msg->set_msg_id(0);
  driver_msg->set_timestamp(0);
  while (apollo::cyber::OK()) {
    auto res = client->SendRequest(driver_msg);
    if (res != nullptr) {
      AINFO << "client: response: " << res->ShortDebugString();
    } else {
      AINFO << "client: service may not ready.";
    }
    sleep(1);
  }

  apollo::cyber::WaitForShutdown();
  return 0;
}
```

## 3. Bazel build file（构建文件）

```cpp
cc_binary(
    name = "service",
    srcs = [ "service.cc", ],
    deps = [
        "//cyber",
        "//cyber/examples/proto:examples_cc_proto",
    ],
)
```

- 编译和运行

```cpp
bazel build cyber/examples/..
```

终端中运行

```cpp
  export GLOG_alsologtostderr=1 
./bazel-bin/cyber/examples/service
```

- 注意事项
  注册服务时，请注意不允许重复的服务名称
  注册服务器和客户端时应用的节点名称也不应重复
- 终端打印结果
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/560557297f1541e3b1b9370394c15bad.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 三、Param parameter service

参数服务被用于节点之间共享的数据，并提供了诸如基本操作set，get和list。参数服务基于Service实现，并包含服务（service）和客户端（client）。

## 支持的数据类型

通过 cyber 传递的所有参数都是[apollo](https://so.csdn.net/so/search?q=apollo&spm=1001.2101.3001.7020)::cyber::Parameter对象，下表列举了5种受支持的参数类型。

> Parameter type | C++ data type | protobuf data type :————- | :————- | :————– apollo::cyber::proto::ParamType::INT | int64_t | int64 apollo::cyber::proto::ParamType::DOUBLE | double | double apollo::cyber::proto::ParamType::BOOL | bool |bool apollo::cyber::proto::ParamType::STRING | std::string | string apollo::cyber::proto::ParamType::PROTOBUF | std::string | string apollo::cyber::proto::ParamType::NOT_SET | - | -

除了上述5种类型外，Parameter还支持使用protobuf对象作为传入参数的接口。执行序列化后处理该对象，并将其转换为STRING类型以进行传输。

## 1. Creating the Parameter Object（创建参数对象）

支持的构造函数：

```cpp
Parameter();  // Name is empty, type is NOT_SET
explicit Parameter(const Parameter& parameter);
explicit Parameter(const std::string& name);  // type为NOT_SET
Parameter(const std::string& name, const bool bool_value);
Parameter(const std::string& name, const int int_value);
Parameter(const std::string& name, const int64_t int_value);
Parameter(const std::string& name, const float double_value);
Parameter(const std::string& name, const double double_value);
Parameter(const std::string& name, const std::string& string_value);
Parameter(const std::string& name, const char* string_value);
Parameter(const std::string& name, const std::string& msg_str,
          const std::string& full_name, const std::string& proto_desc);
Parameter(const std::string& name, const google::protobuf::Message& msg);
```

使用Parameter对象的示例代码：

```cpp
Parameter a("int", 10);
Parameter b("bool", true);
Parameter c("double", 0.1);
Parameter d("string", "cyber");
Parameter e("string", std::string("cyber"));
// proto message Chatter
Chatter chatter;
Parameter f("chatter", chatter);
std::string msg_str("");
chatter.SerializeToString(&msg_str);
std::string msg_desc("");
ProtobufFactory::GetDescriptorString(chatter, &msg_desc);
Parameter g("chatter", msg_str, Chatter::descriptor()->full_name(), msg_desc);
```

- 接口和数据读取

```cpp
inline ParamType type() const;
inline std::string TypeName() const;
inline std::string Descriptor() const;
inline const std::string Name() const;
inline bool AsBool() const;
inline int64_t AsInt64() const;
inline double AsDouble() const;
inline const std::string AsString() const;
std::string DebugString() const;
template <typename Type>
typename std::enable_if<std::is_base_of<google::protobuf::Message, Type>::value, Type>::type
value() const;
template <typename Type>
typename std::enable_if<std::is_integral<Type>::value && !std::is_same<Type, bool>::value, Type>::type
value() const;
template <typename Type>
typename std::enable_if<std::is_floating_point<Type>::value, Type>::type
value() const;
template <typename Type>
typename std::enable_if<std::is_convertible<Type, std::string>::value, const std::string&>::type
value() const;
template <typename Type>
typename std::enable_if<std::is_same<Type, bool>::value, bool>::type
value() const;
```

如何使用这些接口的示例：

```cpp
Parameter a("int", 10);
a.Name();  // return int
a.Type();  // return apollo::cyber::proto::ParamType::INT
a.TypeName();  // return string: INT
a.DebugString();  // return string: {name: "int", type: "INT", value: 10}
int x = a.AsInt64();  // x = 10
x = a.value<int64_t>();  // x = 10
x = a.AsString();  // Undefined behavior, error log prompt
f.TypeName();  // return string: chatter
auto chatter = f.value<Chatter>();
```

## 2. Parameter Service（参数服务端）

如果一个节点要向其他节点提供参数服务，则需要创建一个ParameterService。

```cpp
/**
 * @brief Construct a new ParameterService object
 *
 * @param node shared_ptr of the node handler
 */
explicit ParameterService(const std::shared_ptr<Node>& node);
```

由于所有参数都存储在参数服务对象中，因此可以直接在ParameterService中操纵参数，而无需发送服务请求。

- 设置参数：

```cpp
/**
 * @brief Set the Parameter object
 *
 * @param parameter parameter to be set
 */
void SetParameter(const Parameter& parameter);
```

- 获取参数：

```cpp
/**
 * @brief Get the Parameter object
 *
 * @param param_name
 * @param parameter the pointer to store
 * @return true
 * @return false call service fail or timeout
 */Dreamview的使用
bool GetParameter(const std::string& param_name, Parameter* parameter);
```

- 获取参数列表：

```cpp
/**
 * @brief Get all the Parameter objects
 *
 * @param parameters pointer of vector to store all the parameters
 * @return true
 * @return false call service fail or timeout
 */
bool ListParameters(std::vector<Parameter>* parameters);
```

## 3. Parameter Client（参数客户端）

如果一个节点要使用其他节点的参数服务，则需要创建一个ParameterClient。

```cpp
/**
 * @brief Construct a new ParameterClient object
 *
 * @param node shared_ptr of the node handler
 * @param service_node_name node name which provide a param services
 */
ParameterClient(const std::shared_ptr<Node>& node, const std::string& service_node_name);
```

您还可以执行SetParameter，GetParameter并ListParameters在“ 参数服务”下进行了提及。

## 4. Demo - example（演示-示例）

```cpp
#include "cyber/cyber.h"
#include "cyber/parameter/parameter_client.h"
#include "cyber/parameter/parameter_server.h"

using apollo::cyber::Parameter;
using apollo::cyber::ParameterServer;
using apollo::cyber::ParameterClient;

int main(int argc, char** argv) {
  apollo::cyber::Init(*argv);
  std::shared_ptr<apollo::cyber::Node> node =
      apollo::cyber::CreateNode("parameter");
  auto param_server = std::make_shared<ParameterServer>(node);
  auto param_client = std::make_shared<ParameterClient>(node, "parameter");
  param_server->SetParameter(Parameter("int", 1));
  Parameter parameter;
  param_server->GetParameter("int", &parameter);
  AINFO << "int: " << parameter.AsInt64();
  param_client->SetParameter(Parameter("string", "test"));
  param_client->GetParameter("string", &parameter);
  AINFO << "string: " << parameter.AsString();
  param_client->GetParameter("int", &parameter);
  AINFO << "int: " << parameter.AsInt64();
  return 0;
}
```

- 编译

```cpp
bazel build cyber/examples/..
```

- 终端中运行

```cpp
  export GLOG_alsologtostderr=1 
./bazel-bin/cyber/examples/paramserver
```

- 终端打印结果
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/029cc4ed780b4467b2e7d3d727636b80.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_19,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 四、Log API（日志）

## 1. Log library（日志库）

cyber 日志库建立在glog之上。需要包括以下头文件：

```cpp
#include "cyber/common/log.h"
#include "cyber/init.h"
```

## 2. Log configuration（日志配置）

默认全局配置路径：cyber / setup.bash

以下配置可以由devloper修改：

```cpp
export GLOG_log_dir=/apollo/data/log
export GLOG_alsologtostderr=0
export GLOG_colorlogtostderr=1
export GLOG_minloglevel=0
```

## 3. Log initialization（日志初始化）

在代码条目处调用Init方法以初始化日志：

```cpp
apollo::cyber::cyber::Init(argv[0]) is initialized.
If no macro definition is made in the previous component, the corresponding log is printed to the binary log.
```

## 4. Log output macro（日志输出宏）

日志库封装在日志打印宏中。相关的日志宏的用法如下：

```cpp
ADEBUG << "hello cyber.";
AINFO  << "hello cyber.";
AWARN  << "hello cyber.";
AERROR << "hello cyber.";
AFATAL << "hello cyber.";
```

## 5. Log format（日志格式）

格式为

```cpp
 <MODULE_NAME>.log.<LOG_LEVEL>.<datetime>.<process_id>
```

关于日志文件
当前，与默认glog唯一不同的输出行为是模块的不同日志级别将被写入同一日志文件。

------

# 五、Building a module based on Component（基于组件构建模块）

## 1. 关键概念

### 1.1 Component（组件）

该组件是Cyber RT提供的用于构建应用程序模块的基类。每个特定应用模块可以继承组件类和定义自己Init和Proc功能，因此它可以被加载到 Cyber 框架。

### 1.2 二进制VS组件

有两种选择可将Cyber RT框架用于应用程序：

- 基于二进制：将应用程序单独编译成二进制文件，通过创建自己的Reader和Writer与其他 cyber 模块进行通信。
- 基于组件：将应用程序编译到共享库中。通过继承Component类并编写相应的dag描述文件，Cyber
  RT框架将动态加载和运行应用程序。

## 2. The essential Component interface（基本组件接口）

- 组件的Init()功能类似于执行算法初始化的主要功能。
- 组件的Proc()功能类似于阅读器的回调函数，该函数在消息到达时由框架调用。

## 3. 使用组件的优点

- 可以通过启动文件将组件加载到不同的进程中，并且部署非常灵活。
- 组件可以通过修改dag文件来更改接收到的通道名称，而无需重新编译。
- 组件支持接收多种类型的数据。
- 组件支持提供多种融合策略。

## 4.Dag 文件格式

dag文件示例：

```cpp
# Define all coms in DAG streaming.
module_config {
    module_library : "lib/libperception_component.so"
    components {
        class_name : "PerceptionComponent"
        config {
            name : "perception"
            readers {
                channel: "perception/channel_name"
            }
        }
    }
    timer_components {
        class_name : "DriverComponent"
        config {
            name : "driver"
            interval : 100
        }
    }
}
```

- module_library：如果要加载.so库，根目录是cyber的工作目录（与.so相同的目录setup.bash）。
- components＆timer_component：选择需要加载的基本组件类类型。
- class_name：要加载的组件类的名称。
- name：加载的class_name作为加载示例的标识符。
- readers：当前组件接收的数据，支持1-3个数据通道。

## 5. Demo - examples（演示-例子）

头文件定义（common_component_example.h）

```cpp
#include <memory>

#include "cyber/class_loader/class_loader.h"
#include "cyber/component/component.h"
#include "cyber/examples/proto/examples.pb.h"

using apollo::cyber::examples::proto::Driver;
using apollo::cyber::Component;
using apollo::cyber::ComponentBase;

class Commontestcomponent : public Component<Driver, Driver> {
 public:
  bool Init() override;
  bool Proc(const std::shared_ptr<Driver>& msg0,
            const std::shared_ptr<Driver>& msg1) override;
};
CYBER_REGISTER_COMPONENT(Commontestcomponent)
```

Cpp文件实现（common_component_example.cc）

```cpp
#include "cyber/examples/common_component_smaple/common_component_example.h"

#include "cyber/class_loader/class_loader.h"
#include "cyber/component/component.h"

bool Commontestcomponent::Init() {
  AINFO << "Commontest component init";
  return true;
}

bool Commontestcomponent::Proc(const std::shared_ptr<Driver>& msg0,
                               const std::shared_ptr<Driver>& msg1) {
  AINFO << "Start commontest component Proc [" << msg0->msg_id() << "] ["
        << msg1->msg_id() << "]";
  return true;
}
```

- 编译

```cpp
bazel build cyber/examples/timer_component_smaple/..
```

- 运行

```cpp
mainboard -d cyber/examples/timer_component_smaple/timer.dag
```

注意事项
需要注册组件才能通过SharedLibrary加载类。注册界面如下所示：

```cpp
CYBER_REGISTER_COMPONENT(DriverComponent)
```

如果在注册时使用名称空间，则还需要在dag文件中定义名称空间时添加名称空间。

- Component和TimerComponent的配置文件不同，请注意不要混淆两者。

------

# 六. Launch（启动）

cyber_launch是Cyber RT框架的启动器，它根据启动文件启动多个mainboard，并根据dag文件将不同的组件加载到不同的mainboard中。cyber_launch支持两种方案，可在子进程中动态加载组件或启动二进制程序。

## 启动文件格式

```cpp
<cyber>
    <module>
        <name>driver</name>
        <dag_conf>driver.dag</dag_conf>
        <process_name></process_name>
        <exception_handler>exit</exception_handler>
    </module>
    <module>
        <name>perception</name>
        <dag_conf>perception.dag</dag_conf>
        <process_name></process_name>
        <exception_handler>respawn</exception_handler>
    </module>
    <module>
        <name>planning</name>
        <dag_conf>planning.dag</dag_conf>
        <process_name></process_name>
    </module>
</cyber>
```

模块：每个加载的组件或二进制文件都是一个模块

- name是加载的模块名称
- dag_conf是组件的相应dag文件的名称
- process_name是mainboard进程启动的名称，process_name的相同组件将被加载并在同一进程中运行。
- exception_handler是流程中发生异常时的处理程序方法。该值可以是下面列出的退出或重新生成。
  - exit，这意味着当当前进程异常退出时，整个进程需要停止运行。
  - respawn，异常退出后需要重新启动当前进程。可由用户根据过程的具体条件进行控制。

------

# 七. Timer（定时器）

可用于创建定时任务以定期运行或仅运行一次。

```cpp
/**
 * @brief Construct a new Timer object
 *
 * @param period The period of the timer, unit is ms
 * @param callback The tasks that the timer needs to perform
 * @param oneshot True: perform the callback only after the first timing cycle
 *                False: perform the callback every timed period
 */
Timer(uint32_t period, std::function<void()> callback, bool oneshot);
```

或者可以将参数封装到计时器选项中，如下所示：

```cpp
struct TimerOption {
  uint32_t period;                 // The period of the timer, unit is ms
  std::function<void()> callback;  // The tasks that the timer needs to perform
  bool oneshot;  // True: perform the callback only after the first timing cycle
                 // False: perform the callback every timed period
};
/**
 * @brief Construct a new Timer object
 *
 * @param opt Timer option
 */
explicit Timer(TimerOption opt);
```

## 1. 启动定时器

创建 Timer 实例后，您必须调用Timer::Start()以启动计时器。

## 2. 停止定时器

当需要手动停止已经启动的定时器时，可以调用该Timer::Stop()接口。

## 3. Demo - example（演示 - 示例）

```cpp
#include <iostream>
#include "cyber/cyber.h"
int main(int argc, char** argv) {
    cyber::Init(argv[0]);
    // Print current time every 100ms
    cyber::Timer timer(100, [](){
        std::cout << cyber::Time::Now() << std::endl;
    }, false);
    timer.Start()
    sleep(1);
    timer.Stop();
}
```

## 4. 时间接口

Time是一个用来管理时间的类；可用于当前时间获取、耗时计算、时间转换等。

时间接口如下：

```cpp
// constructor, passing in a different value to construct Time
Time(uint64_t nanoseconds); //uint64_t, in nanoseconds
Time(int nanoseconds); // int type, unit: nanoseconds
Time(double seconds); // double, in seconds
Time(uint32_t seconds, uint32_t nanoseconds);
// seconds seconds + nanoseconds nanoseconds
Static Time Now(); // Get the current time
Double ToSecond() const; // convert to seconds
Uint64_t ToNanosecond() const; // Convert to nanoseconds
Std::string ToString() const; // Convert to a string in the format "2018-07-10 20:21:51.123456789"
Bool IsZero() const; // Determine if the time is 0
```

代码示例如下：

```cpp
#include <iostream>
#include "cyber/cyber.h"
#include "cyber/duration.h"
int main(int argc, char** argv) {
    cyber::Init(argv[0]);
    Time t1(1531225311123456789UL);
    std::cout << t1.ToString() << std::endl; // 2018-07-10 20:21:51.123456789
    // Duration time interval
    Time t1(100);
    Duration d(200);
    Time t2(300);
    assert(d == (t1-t2)); // true
}
```

# 八、Record file: Read and Write（录制文件：读取与写入）

RecordReader是用于在网络框架中读取消息的组件。每个 RecordReader 都可以通过该Open方法打开一个已经存在的记录文件，线程会异步读取记录文件中的信息。用户只需在RecordReader中执行ReadMessage提取最新消息，然后通过GetCurrentMessageChannelName、GetCurrentRawMessage、GetCurrentMessageTime获取消息信息即可。

RecordWriter是用于在网络框架中记录消息的组件。每个 RecordWriter 都可以通过 Open 方法创建一个新的记录文件。用户只需要执行WriteMessage和WriteChannel即可写入消息和通道信息，写入过程是异步的。

## Demo - example(cyber/examples/record.cc)

将 100 个 RawMessage 通过test_write方法写入TEST_FILE，然后通过方法将它们读出test_read。

```cpp
#include <string>

#include "cyber/cyber.h"
#include "cyber/message/raw_message.h"
#include "cyber/proto/record.pb.h"
#include "cyber/record/record_message.h"
#include "cyber/record/record_reader.h"
#include "cyber/record/record_writer.h"

using ::apollo::cyber::record::RecordReader;
using ::apollo::cyber::record::RecordWriter;
using ::apollo::cyber::record::RecordMessage;
using apollo::cyber::message::RawMessage;

const char CHANNEL_NAME_1[] = "/test/channel1";
const char CHANNEL_NAME_2[] = "/test/channel2";
const char MESSAGE_TYPE_1[] = "apollo.cyber.proto.Test";
const char MESSAGE_TYPE_2[] = "apollo.cyber.proto.Channel";
const char PROTO_DESC[] = "1234567890";
const char STR_10B[] = "1234567890";
const char TEST_FILE[] = "test.record";

void test_write(const std::string &writefile) {
  RecordWriter writer;
  writer.SetSizeOfFileSegmentation(0);
  writer.SetIntervalOfFileSegmentation(0);
  writer.Open(writefile);
  writer.WriteChannel(CHANNEL_NAME_1, MESSAGE_TYPE_1, PROTO_DESC);
  for (uint32_t i = 0; i < 100; ++i) {
    auto msg = std::make_shared<RawMessage>("abc" + std::to_string(i));
    writer.WriteMessage(CHANNEL_NAME_1, msg, 888 + i);
  }
  writer.Close();
}

void test_read(const std::string &readfile) {
  RecordReader reader(readfile);
  RecordMessage message;
  uint64_t msg_count = reader.GetMessageNumber(CHANNEL_NAME_1);
  AINFO << "MSGTYPE: " << reader.GetMessageType(CHANNEL_NAME_1);
  AINFO << "MSGDESC: " << reader.GetProtoDesc(CHANNEL_NAME_1);

  // read all message
  uint64_t i = 0;
  uint64_t valid = 0;
  for (i = 0; i < msg_count; ++i) {
    if (reader.ReadMessage(&message)) {
      AINFO << "msg[" << i << "]-> "
            << "channel name: " << message.channel_name
            << "; content: " << message.content
            << "; msg time: " << message.time;
      valid++;
    } else {
      AERROR << "read msg[" << i << "] failed";
    }
  }
  AINFO << "static msg=================";
  AINFO << "MSG validmsg:totalcount: " << valid << ":" << msg_count;
}

int main(int argc, char *argv[]) {
  apollo::cyber::Init(argv[0]);
  test_write(TEST_FILE);
  sleep(1);
  test_read(TEST_FILE);
  return 0;
}
```

- 构建

```cpp
bazel build cyber/examples/..
```

- 运行

```cpp
./bazel-bin/cyber/examples/record
```

- 测试结果

> I1124 16:56:27.248200 15118 record.cc:64] [record] msg[0]-> channel name: /test/channel1; content: abc0; msg time: 888
> I1124 16:56:27.248227 15118 record.cc:64] [record] msg[1]-> channel name: /test/channel1; content: abc1; msg time: 889
> I1124 16:56:27.248239 15118 record.cc:64] [record] msg[2]-> channel name: /test/channel1; content: abc2; msg time: 890
> I1124 16:56:27.248252 15118 record.cc:64] [record] msg[3]-> channel name: /test/channel1; content: abc3; msg time: 891
> I1124 16:56:27.248297 15118 record.cc:64] [record] msg[4]-> channel name: /test/channel1; content: abc4; msg time: 892
> I1124 16:56:27.248378 15118 record.cc:64] [record] msg[5]-> channel name: /test/channel1; content: abc5; msg time: 893
> …
> I1124 16:56:27.250422 15118 record.cc:73] [record] static msg=================
> I1124 16:56:27.250434 15118 record.cc:74] [record] MSG validmsg:totalcount: 100:100

# 九、 API Directory（API目录）

查阅[Cyber RT API tutorial](https://cyber-rt.readthedocs.io/en/latest/CyberRT_API_for_Developers.html#create-a-node)

------

# 参考

[Cyber RT API tutorial](https://cyber-rt.readthedocs.io/en/latest/CyberRT_API_for_Developers.html#create-a-node)