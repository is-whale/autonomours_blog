- [路径规划算法学习笔记8-RRT - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/146924028)

**一、原理：**

Rapidly-exploring Random Tree: Building up a tree through generating "next states" in the tree by executing random controls.

![img](https://pic2.zhimg.com/80/v2-9f4f14943f1a23911a44534edbd53f9d_720w.jpg)

**说明：**首先，在空间中随机采样一个节点 xrand ,然后在当前的树中找到距离该随机点最近的节点 xnear ,以固定步长从节点 xnear 向 xrand 的方向拓展一步，得到新的节点 xnew ,接下来判断拓展边 xnearxnew 是否会和障碍物发生碰撞，如果不会，则新节点被添加到树中，如果发生碰撞，则该节点会被舍弃。[[1\]](https://zhuanlan.zhihu.com/p/146924028#ref_1)

![img](https://pic3.zhimg.com/80/v2-22531eb73d18f28fc678d2cd9632793e_720w.jpg)

**二、优缺点：**

优势：

Aims to find a path from the start to the goal.

More target-oriented than PRM.

劣势：

Not optimal solution.

Not efficient(leave room for improvement).

Sample in the whole space.

**三、改进算法**

**(1). Kd-tree:**

参考 [https://blog.csdn.net/junshen1314/article/details/51121582](https://link.zhihu.com/?target=https%3A//blog.csdn.net/junshen1314/article/details/51121582)

**(2). Bidirectional RRT/RRT Connect:** Grow a tree from both the start point and the goal point. Path finding when two trees are connected.

![img](https://pic3.zhimg.com/80/v2-b766be8261a9a6110a812d414f1a4832_720w.jpg)



**四 代码**[[2\]](https://zhuanlan.zhihu.com/p/146924028#ref_2)

```python
import matplotlib.pyplot as plt
import random
import math
import copy

show_animation = True


class Node(object):
    """
    RRT Node
    """

    def __init__(self, x, y):
        self.x = x
        self.y = y
        self.parent = None


class RRT(object):
    """
    Class for RRT Planning
    """

    def __init__(self, start, goal, obstacle_list, rand_area):
        """
        Setting Parameter

        start:Start Position [x,y]
        goal:Goal Position [x,y]
        obstacleList:obstacle Positions [[x,y,size],...]
        randArea:random sampling Area [min,max]

        """
        self.start = Node(start[0], start[1])
        self.end = Node(goal[0], goal[1])
        self.min_rand = rand_area[0]
        self.max_rand = rand_area[1]
        self.expandDis = 1.0
        self.goalSampleRate = 0.05  # 选择终点的概率是0.05
        self.maxIter = 500
        self.obstacleList = obstacle_list
        self.nodeList = [self.start]

    def random_node(self):
        """
        产生随机节点
        :return:
        """
        node_x = random.uniform(self.min_rand, self.max_rand)  # random.uniform() 随机生成一个实数，它在[x,y]范围内
        node_y = random.uniform(self.min_rand, self.max_rand)
        node = [node_x, node_y]

        return node

    @staticmethod
    def get_nearest_list_index(node_list, rnd):
        """
        :param node_list:
        :param rnd:
        :return:
        """
        d_list = [(node.x - rnd[0]) ** 2 + (node.y - rnd[1]) ** 2 for node in node_list]
        min_index = d_list.index(min(d_list))
        return min_index

    @staticmethod
    def collision_check(new_node, obstacle_list):
        a = 1
        for (ox, oy, size) in obstacle_list:
            dx = ox - new_node.x
            dy = oy - new_node.y
            d = math.sqrt(dx * dx + dy * dy)
            if d <= size:
                a = 0  # collision

        return a  # safe

    def planning(self):
        """
        Path planning

        animation: flag for animation on or off
        """

        while True:
            # Random Sampling
            if random.random() > self.goalSampleRate:
                rnd = self.random_node()
            else:
                rnd = [self.end.x, self.end.y]

            # Find nearest node
            min_index = self.get_nearest_list_index(self.nodeList, rnd)
            # print(min_index)

            # expand tree
            nearest_node = self.nodeList[min_index]

            # 返回弧度制
            theta = math.atan2(rnd[1] - nearest_node.y, rnd[0] - nearest_node.x)

            new_node = copy.deepcopy(nearest_node)
            new_node.x += self.expandDis * math.cos(theta)
            new_node.y += self.expandDis * math.sin(theta)
            new_node.parent = min_index

            if not self.collision_check(new_node, self.obstacleList):
                continue

            self.nodeList.append(new_node)

            # check goal
            dx = new_node.x - self.end.x
            dy = new_node.y - self.end.y
            d = math.sqrt(dx * dx + dy * dy)
            if d <= self.expandDis:
                print("Goal!!")
                break

            if True:
                self.draw_graph(rnd)

        path = [[self.end.x, self.end.y]]
        last_index = len(self.nodeList) - 1
        while self.nodeList[last_index].parent is not None:
            node = self.nodeList[last_index]
            path.append([node.x, node.y])
            last_index = node.parent
        path.append([self.start.x, self.start.y])

        return path

    def draw_graph(self, rnd=None):
        """
        Draw Graph
        """
        print('aaa')
        plt.clf()  # 清除上次画的图
        if rnd is not None:
            plt.plot(rnd[0], rnd[1], "^g")
        for node in self.nodeList:
            if node.parent is not None:
                plt.plot([node.x, self.nodeList[node.parent].x], [
                         node.y, self.nodeList[node.parent].y], "-g")

        for (ox, oy, size) in self.obstacleList:
            plt.plot(ox, oy, "sk", ms=10*size)

        plt.plot(self.start.x, self.start.y, "^r")
        plt.plot(self.end.x, self.end.y, "^b")
        plt.axis([self.min_rand, self.max_rand, self.min_rand, self.max_rand])
        plt.grid(True)
        plt.pause(0.01)

    def draw_static(self, path):
        """
        画出静态图像
        :return:
        """
        plt.clf()  # 清除上次画的图

        for node in self.nodeList:
            if node.parent is not None:
                plt.plot([node.x, self.nodeList[node.parent].x], [
                    node.y, self.nodeList[node.parent].y], "-g")

        for (ox, oy, size) in self.obstacleList:
            plt.plot(ox, oy, "sk", ms=10*size)

        plt.plot(self.start.x, self.start.y, "^r")
        plt.plot(self.end.x, self.end.y, "^b")
        plt.axis([self.min_rand, self.max_rand, self.min_rand, self.max_rand])

        plt.plot([data[0] for data in path], [data[1] for data in path], '-r')
        plt.grid(True)
        plt.show()


def main():
    print("start RRT path planning")

    obstacle_list = [
        (5, 1, 1),
        (3, 6, 2),
        (3, 8, 2),
        (1, 1, 2),
        (3, 5, 2),
        (9, 5, 2)]

    # Set Initial parameters
    rrt = RRT(start=[0, 0], goal=[3, 3], rand_area=[-5, 5], obstacle_list=obstacle_list)
    path = rrt.planning()
    print(path)

    # Draw final path
    if show_animation:
        plt.close()
        rrt.draw_static(path)


if __name__ == '__main__':
    main()
```

## 参考

1. [^](https://zhuanlan.zhihu.com/p/146924028#ref_1_0)https://blog.csdn.net/songyunli1111/article/details/81982298
2. [^](https://zhuanlan.zhihu.com/p/146924028#ref_2_0)https://www.cnblogs.com/clemente/p/9543106.html