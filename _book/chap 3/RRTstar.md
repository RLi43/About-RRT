# 3.1 RRT*

与RRT相比，RRT\*增加了重新布线的步骤，这使得它满足渐进最优（但同时速度也大大下降）。

重新布线包括重新选择父节点和近邻重新布线的过程。[运动规划RRT*算法图解](https://blog.csdn.net/weixin_43795921/article/details/88557317)提供了图像理解重新选择父节点的过程，其目的在于使新点的树路径距离尽可能优，近邻重新布线过程相当于对附近点试探新点作为父节点是否能改善附近点的树路径距离。如果这个近邻的距离r是无穷大，相当于建立了一棵dijkstra树，但是这样计算代价很大，只取新增的点附近进行重新布线。这个“附近”的半径是由其他研究确定的能够实现渐进最优的下界。

为了便于下面的叙述，符号化定义上面提到的一些概念。对于一棵树$$T$$，其上的顶点用$$v$$表示，边（有向）用$$e(v_1,v_2)$$表示，$$v_1$$是父节点。后续出现的$$x$$表示空间中的一个点，可能在树上，也可能不在；同样，$$e(x_1,x_2)$$可能是树上的一条边，也可能不是。

记$$g_T(v)$$为从根节点（也就是起点$$x_I$$）出发到树上顶点$$v$$的路径距离。若某一点$$x$$不在树上，则$$g_T(x)=\infty$$

记$$c(x_1,x_2)$$为边$$e(x_1,x_2)$$的代价，由于本文中的代价就是距离，在此边不碰撞时，$$c(x_1,x_2)=\hat{c}(x_1,x_2)=distance(x_1,x_2)$$，若此边碰撞$$c(x_1,x_2)=\infty$$。

那么RRT\*的重选父节点过程可以表示为：

```python
for v in V_near:
	if g_T(v)+c(v,x_new) < g_T(x_new):
        deleteEdge(x_new->parent,x_new)
    	addEdge(v,x_new)
```

![](https://img-blog.csdnimg.cn/20190314172202562.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc5NTkyMQ==,size_16,color_FFFFFF,t_70)对附近进行重新布线的过程可以表示为：

```python
for v in V_near:
	if g_T(x_new)+c(x_new,v) < g_T(v):
        deleteEdge(v->parent,v)
    	addEdge(x_new,v)
```



为了提速，也可以像RRT-Connect/BiRRT一样，用两棵树，即BiRRT\*。