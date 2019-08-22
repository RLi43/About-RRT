# 2.1 RRT

“只见树木，不见森林”在一般的语境是贬义的，但是在算法中也不尽然。“见森林”的代价可能是很高的，而仅仅判断一段线段上是否有树木将会轻松很多。RRT的主要思想就是路径树在空间中随机扩展，不断试探扩展的可能性来构建一棵树。

## RRT算法

[TODO：这里有一张GIF]

现实中的环境基本上会比这个简单的演示要复杂很多，光是表示这些障碍物就很复杂，根据障碍物信息来寻找路径就更难了。RRT的意图就是抛开了解全局的复杂，只用简单的点碰撞检测来探索空间，寻找路径。

伪代码：

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
            T.AddNode(qnew) # 成功添加扩展点
    return false
```

RRT通过随机的Sample()得到一个点qrand，找一个树上离qrand最近的点qnear，从qnear出发向qrand移动Δq得到qnew，若没有发生碰撞（有效点）则向树添加这个点。直到找到点与目标距离足够近，即获得了路径。

那么你可能有一个问题：为什么要找一个最近的点进行扩张？而不是随机的一个点呢？ 这是因为，如果是随机点，随机方向扩展，这棵树的扩展速度十分有限，下图就是两种方法在相同的步骤数后生成的树的对比。

![1565834745959](C:\Users\Robot.Li\AppData\Roaming\Typora\typora-user-images\1565834745959.png)

## RRT的改进与思考

### 加速！

如果一个规划对时间的要求不高，完全可以用解析的方法细细地找。选择RRT，只是在概率上完备的方法来计算规划，显然是因为任务需要较快地解决。那么如何让RRT更快呢？

#### 采样偏置

为了加快随机树到达目标点的速度，简单的改进方法就是让树朝着目标生长。

具体来说，在随机树每次的生长过程中，根据随机概率来决定qrand是目标点还是随机点。在Sample函数中设定参数Prob，每次得到一个0到1.0的随机值p，当0<p<Prob的时候，随机树朝目标点生长行；当Prob<p<1.0时，随机树朝一个随机方向生长。

```python
def Sample(): 
     p = Random(0, 1.0)
     if 0 < p < Prob :
         return qgoal
     elif Prob < p < 1.0 :
         return RandomNode()
```

#### 双向搜索

也可以用两棵树来加速。这部分的内容比较多，整理了一个新的小节[RRTConnect](chap 2/RRTConnect.md)。这是一个非常有效的算法，能够使求解加速十分显著。

#### 数据结构

RRT算法需要寻找最近的树上顶点，可以采用`k-d Tree`结构来存储顶点，提高搜索效率。

## RRT的缺点

RRT算法也有一些缺点，它是一种纯粹的随机搜索算法对环境类型不敏感。当C-空间中包含大量障碍物或狭窄通道约束时，算法的收敛速度慢，效率会大幅下降。因为狭窄通道面积小，被碰到的概率低。

## 参考文献

[1] Steven M. LaValle, PLANNING ALGORITHMS

[2] Steven M. LaValle, Rapidly-Exploring Random Trees: A New Tool for Path Planning