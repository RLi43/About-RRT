# 3 渐进最优的RRT们

理论上，随着搜索时间的增加，RRT找到解的可能性趋于100%。但我们也希望能找的最优的解，RRT\*应运而生。通过一系列地优化步骤，RRT\*满足渐近最优，即，随着搜索时间的增加，RRT\*找到最优解的可能性趋于100%。

本章节将介绍RRT\*及其衍生的各种更有效更快速的版本。