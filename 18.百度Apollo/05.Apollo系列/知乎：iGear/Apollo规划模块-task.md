- [Apollo规划模块-task - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/427199621)

## 引

这篇会梳理一下apollo planning模块中 scenario-stage-task三件套的最后一部分task。task指一个在一个stage下车辆规划的具体的处理方法。

## **相关分析**

在apollo/modules/planning/tasks文件夹中，Task分为3类：deciders(决策)，optimizers（规划），learning_model。task.h定义了Task基类，其中重要的是2个Execute()函数。向上而言，这两个函数会在前序的stage部分被ExecuteTaskOnReferenceLine以执行task的具体逻辑。

在具体Execute继承而言decider/decider.h中定义了Decider类，继承自Task类，对应着继承而来的2个Execute()，分别定义了2个Process()，即Task的执行是通过Decider::Process()运行的。learning_model 与 optimizers也是类似的继承逻辑。

***不过这里有几个decider是例外的是直接继承了Task类的而不是继承Decider类，例如PathDecider与SpeedDecider***

在deciders(决策)模块中apollo中apollo实现了14个deciders(决策)任务，optimizers（规划）实现了三个Tasks的基本大类，PathOptimizer，SpeedOptimizer，TrajectoryOptimizer。这3种Optimizer实现了各自的Execute()，并定义了纯虚函数Process()在Execute()中被调用，由子类实现具体的Process()。不同的优化器，如PiecewiseJerkPathOptimizer是继承自PathOptimizer的，实现自己的Process()。

![img](https://pic4.zhimg.com/80/v2-e59f8eecd441bb078f24ffb39ac4586f_720w.jpg)

## **小结**

apollo规划模块中 scenario-stage-task三件套已全部介绍完毕