# 2 RRT

## 基于采样的规划

连续空间的运动规划常见的有两大类，基于采样的运动规划，组合运动规划。本书只谈论一部分基于采样的算法。

为什么用采样？前面提到，机械臂的运动规划在configuration space中，对于最常见的六轴机器人，空间的维度就是6。如果将整个空间离散化，实际上是一个很高的计算负担。而对于像蛇形机器人这种大量关节的结构，configuration space就更加复杂了。采样的优点在于对维度不敏感。

## Rapid Random-Exploration Tree

[RRT路径规划算法](https://www.cnblogs.com/21207-iHome/p/7210543.html)这篇文章已经对RRT有了较好的介绍，下面的介绍大部分基于此文。如果对实现的算法有兴趣，博文中提供了Matlab代码，在[GitHub](https://github.com/AtsushiSakai/PythonRobotics/tree/master/PathPlanning)上有很多Python的实现。如果文中的描述过于抽象，需要图像的辅助，这里有个[童趣的版本](https://jingyan.baidu.com/article/574c521957eb406c8d9dc1bc.html)。

### 算法

### 一些改进

#### 目标引导

#### 两棵树





## 参考文献

[1] Steven M. LaValle, PLANNING ALGORITHMS

[2] Steven M. LaValle, Rapidly-Exploring Random Trees: A New Tool for Path Planning