# 1.2 离散规划

在开始现实连续空间之前，不妨先从离散规划中了解一些常见的规划。如果您有计算机背景，那么将会对以下的图寻路算法比较熟悉。这里选择的是访问图的算法，因为在一些规划场景中，图是需要探索的（如果需要比喻，战场迷雾）。

#### 前向搜索

FORWARD_SEARCH

```python
Q.Insert(x_I) and mark x_I as visited
while Q not empty do
    x = Q.pop()
    if x in X_G
    	return SUCCESS
    for all u in U(x)
    	x_new = f(x,u)
        if x_new not visited
            Mark x_new as visited
            Q.Insert(x_new)
		else
        	Resolve duplicate x_new
return FAILURE
```

从起点开始，逐个挑选点访问，生成路径树。Q是一个优先队列，它决定了访问的顺序，许多搜索算法的唯一显著的区别就是Q的排序方法。对最优规划来说，选择合适的排序方式，让构成最优解的顶点尽快（当然，首先要求能）被搜索到。

深度优先搜索DFS的Q是LIFO（栈），广度优先搜索BFS的Q是FIFO（顺序队列）；

大名鼎鼎的Dijkstra，Q的value就是cost-to-come/C(x)，i.e. 从起点到当前点的树路径长度，这个代价在加入新的点时会不断更新。它是单源最优的。

与Dijkstra相对，Best First算法对cost-to-go/G(x)进行排序，也就是根据到终点的距离选择。当然，作为一棵正在生长的树，到终点的距离实际上是不可知的，所以这里用的是距离估计，一般是距离的下界。这个算法一般很快，但很可能不是最优的，最坏的情况下会很慢（比如一头扎进死路）。

对Dijkstra进行扩展得到的A-star/A\*算法结合了二者，以f(x)=C(x)+G\*(x)为Q队列value。以终点为目标，更有目的性以减少探索量。

#### 其他常见搜索结构

除了前向搜索，还有多种搜索结构，如后向搜索，双向搜索。后向搜索从终点开始（还记得小时候走迷宫从终点开始找路吗？），这种结构对于起点附近分叉较多的情况比较有效。双向搜索一般都可以降低搜索的范围，问题是在于如何让两棵树快速遇上。