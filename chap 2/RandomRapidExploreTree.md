# 2 基于采样的规划RRT

连续空间的运动规划常见的有两大类，基于采样的运动规划，组合运动规划。本文只谈论一部分基于采样的算法。

如何描述机器人的运动？在前面一章中介绍了configuration space，一个n轴的机器人用一个n维向量就可以描述它的状态。为了描述机器人与环境的关系，一些做法是对环境建模，并在机器人空间表示。但建模的复杂度随n指数上升，对于多轴机器人，如蛇形机器人而言，计算的代价非常高。利用采样进行碰撞检测的方法，可以有效避免对复杂环境的建模，对维度的适应性较好。

[RRT路径规划算法](https://www.cnblogs.com/21207-iHome/p/7210543.html)这篇文章已经对RRT有了较好的介绍，下面的介绍内容基本来自于这篇文章。如果对实现的算法有兴趣，博文中提供了Matlab代码，在[GitHub](https://github.com/AtsushiSakai/PythonRobotics/tree/master/PathPlanning)上有很多Python的实现。如果文中的描述过于抽象，需要图像的辅助，这里有个[童趣的版本](https://jingyan.baidu.com/article/574c521957eb406c8d9dc1bc.html)。

## RRT算法

```python
def RRT(qinit, K, Δq):
	T.init(qinit)
 	for k = 1 to K :
		qrand = RandomSample()  # 随机采样
        qnearest = Nearest(T, qrand) # 选择树上离采样点最近的顶点
        if  Distance(qnearest, qgoal) < Threshold : # 到达终点？
        	return true
        qnew = Extend(qnearest, qrand, Δq)  # 从qnearest向qrand扩展一定距离
        if qnew ≠ NULL :
            T.AddNode(qnew) # 添加扩展点
    return false
```

RRT通过随机的Sample()得到一个点qrand，找一个树上离qrand最近的点qnear，从qnear出发向qrand移动Δq得到qnew，若没有发生碰撞（有效点）则向树添加这个点。直到找到点与目标距离足够近，即获得了路径。

那么你可能有一个问题：为什么要找一个最近的点进行扩张？而不是随机的一个点呢？ 这是因为，如果是随机点，随机方向扩展，这棵树的扩展速度十分有限，下图就是两种方法在相同的步骤数后生成的树的对比。

![1565834745959](C:\Users\Robot.Li\AppData\Roaming\Typora\typora-user-images\1565834745959.png)



```python

def Sample(): 
     p = Random(0, 1.0)
     if 0 < p < Prob :
         return qgoal
     elif Prob < p < 1.0 :
         return RandomNode()

```



## 参考文献

[1] Steven M. LaValle, PLANNING ALGORITHMS

[2] Steven M. LaValle, Rapidly-Exploring Random Trees: A New Tool for Path Planning