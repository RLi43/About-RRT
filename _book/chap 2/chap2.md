# 2 基于采样的规划RRT

连续空间的运动规划常见的有两大类，基于采样的运动规划，组合运动规划。本文只谈论一部分基于采样的算法。

如何描述机器人的运动？在前面一章中介绍了configuration space，一个n轴的机器人用一个n维向量就可以描述它的状态。为了描述机器人与环境的关系，一些做法是对环境建模，并在机器人空间表示。但建模的复杂度随n指数上升，对于多轴机器人，如蛇形机器人而言，计算的代价非常高。采用采样进行碰撞检测的方法，可以有效避免对复杂环境的建模，对维度的适应性较好。

[RRT路径规划算法](https://www.cnblogs.com/21207-iHome/p/7210543.html)这篇文章已经对RRT有了较好的介绍，下面的介绍内容基本来自于这篇文章。如果对实现的算法有兴趣，博文中提供了Matlab代码，在[GitHub](https://github.com/AtsushiSakai/PythonRobotics/tree/master/PathPlanning)上有很多Python的实现。如果文中的描述过于抽象，需要图像的辅助，这里有个[童趣的版本](https://jingyan.baidu.com/article/574c521957eb406c8d9dc1bc.html)。