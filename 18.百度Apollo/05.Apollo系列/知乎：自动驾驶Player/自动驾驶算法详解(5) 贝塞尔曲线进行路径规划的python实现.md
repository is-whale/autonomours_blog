- [自动驾驶算法详解(5): 贝塞尔曲线进行路径规划的python实现 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/514757597)

一、理论知识

1、路径规划定义

路径规划智能物流、无人驾驶等智能领域中重要的组成部分。路径规划的目标是实现从目的地到终点之间寻找一条安全（无碰撞）、高效（最短距离或 最短时间）的一条最优或接近最优的路径。在自动驾驶算法中，路径规划一般特指局部路径规划，即规划一条路径，引导自车从当前位置驶向导航routing模块期望的位置。

自动驾驶中路径规划不仅要考虑一条安全（无碰撞）、高效（最短距离或 最短时间），还应该考虑自车动力学的约束，以及曲线的平滑。

贝塞尔曲线是一种常用的求解自动驾驶规划路径的方法。

2、贝塞尔曲线理论

Bezier贝塞尔曲线是由伯恩斯坦多项式发展而来的， 控制简单，能够处理光滑曲线，应用较为广泛。

![img](https://pic3.zhimg.com/80/v2-b6de09b6eb5583bdcbe0a18842ff178a_720w.jpg)

贝塞尔曲线方法是法国工程师 Bezier 在 1962 年为了设计汽车车身形状提出的，之后贝塞尔曲线由于具 有良好的数学特性而被广泛应用到车辆路径规划领域。

如下图 2 所示，五个控制点可以唯一确定平面内一条四阶贝塞尔曲线，曲线具有仿射变换不变的特性，其参 数化表达式如下：

![img](https://pic3.zhimg.com/80/v2-08be0cc12898b5077655b7ef6dfdfcbe_720w.png)



![img](https://pic1.zhimg.com/80/v2-2e762b8f65a919b6f2925877027bf71c_720w.jpg)

在实际应用中，根据障碍物、期望位置、曲率限制等条件确定控制点，从而求解贝塞尔曲线获得规划的路径。

二、代码实现

```python
import matplotlib.pyplot as plt
import numpy as np
import scipy.special


show_animation = True


start_x = 0 # [m]
start_y = -5 # [m]
start_yaw = 0 # [rad]
end_x = 40 # [m]
end_y = 5 # [m]
end_yaw = 0 # [rad]
control_points = np.array([[0,-5],[20,-5],[20,5],[40,5]])
n_points = 100


def bernstein_poly(n, i, t): 
 return scipy.special.comb(n, i) * t ** i * (1 - t) ** (n - i)




def bezier(t, control_points): 
    n = len(control_points) - 1
 return np.sum([bernstein_poly(n, i, t) * control_points[i] for i in range(n + 1)], axis=0)


traj = []


for t in np.linspace(0, 1, n_points):
    traj.append(bezier(t, control_points))


path = np.array(traj)
```

三、结果可视化

1、控制点



![img](https://pic3.zhimg.com/80/v2-7a24a9eaff050413f64ece0093be848e_720w.jpg)



2、控制点

![img](https://pic3.zhimg.com/80/v2-d56dbf4b3f9de09a342e7335de698f8e_720w.jpg)