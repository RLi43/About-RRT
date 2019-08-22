# 3.3.2 BIT*

BIT\*是Batch Informed Trees的简称，融合了FMT和RRT*结构，并使用Informed启发。

```python
for i in range(iterMaxNum)
    if Qe.empty() and Qv.empty() # new batch
        Prune(g_t(x_g))
        X_samples.addSample(m,g_t(x_g))
        V_old <- V
        Qv <- V
        r = radius(|V|+|X_samples|)
	
    while BestQueueValue(Qv) <= BestQueueValue(Qe) 
        ExpandVertex(BestInQueue(Qv))

    if g_t(v) + c_hat(v,x) + h_hat(x) < g_t(x_g)
        if g_hat(v) + c(v,x) + h_hat(x) < g_t(x_g)
            if g_t(v) + c(v,x) < g_t(x)
                if x in Vertices # rewire
                    Edges.delete(x,v_any)
                else # new vertex
                    Vertices.add(x), X_samples.delete(x)
                    QueueVertex.add(x)
                Edeges.add((v,x)) # successfully expand an edge
                QueueEdge.delete((v_any,x) that g_t(v_any)+c_hat(v_any,x) >= g_t(x)) # prune

def ExpandVertex(v)
    Qv.delete(v)
    X_near ={x in X_samples that ||x-v||<=r}
    Qe.add((v,x) that x in X_near and g_hat(v) + c_hat(v,x) + h_hat(x) < g_t(x_g))
    if v is new in this batch (v is not in V_old):
        V_near = {v in Vertices that ||w-v||<=r}
        Qe.add((v,w) that g_hat(v) + c_hat(v,w) + h_hat(w) < g_T(x_g) and g_t(v) + c_hat(v,w) < g_t(w))
```

BIT\*算法最大的不同之处在于批处理。Informed RRT\*可以视为每批只有一个采样点的BIT\*，这意味着，BIT\*会同时处理大量的采样点。之前介绍的RRT们都只有一个采样点，所以每次都以那个采样点为指示进行扩展；BIT\*中一次进行一批采样，所以增加了选择采样点的步骤。

![BITstar](http://lijiangfan.xyz/wp-content/uploads/2019/08/BITstar.png)

需要指明的是，在RRT中，我们以$$x_{new}$$表示新形成的边，这是由于我们默认了父节点就是$$v_{nearest}$$，所以更加准确的表示是边$$(v_{nearest},x_{new})$$。这很重要，因为BIT\*也选择从何处新生一个枝条（而不是默认最近的）。

有了选择，自然要进行比较。首先我们来做一些定义。记$$f(e(x_1,x_2))$$为经过边$$e(x_1,x_2)$$的解的代价，$$\hat{f}(e(x_1,x_2))$$为所有经过边$$e(x_1,x_2)$$的解的最小代价，$$\hat{f}(x_1,x_2)=\hat{g}(x_1)+\hat{c}(x_1,x_2)+\hat{g}(x_2)$$。对于树$$T$$，记$$f_T(e(x_1,x_2))=g_T(x_1)+c(x_1,x_2)+h(x_2)$$，但是由于$$h(x)$$尚未探索到，故记$$\hat{f_T}=g_T(x_1)+c(x_1,x_2)+\hat{h}(x_2)$$。对于当前的树来说，一条边的价值可以用$$\hat{f_T}$$估计。由于$$c$$的计算涉及碰撞检测比较耗时，故先以$$\hat{c}$$作为估计；另外一棵树的扩展必然从某个顶点开始，故记$$QueueValue(v,x)=g_T(v)+\hat{c}(v,x)+\hat h(x)$$。由$$(v,x)$$和它们的$$QueueValue$$组成一个优先队列$$Q_E$$，按照$$QueueValue$$从小到大排列。

![queueValue(Qe)](http://lijiangfan.xyz/wp-content/uploads/2019/08/queueValueQe.png)

BIT\*算法每一次迭代都在$$Q_E$$中取出最佳估计$$bestEdge(v_m,x_m)$$，检查该边是否有可能改善当前的解，即$$\hat{f_T}(e(v_m,x_m))<bestCost$$，如果可以，那么加入到树中。增加之后对周围进行重新布线（如果想省时间也可以等到一批结束之后再检查重新布线）。

这就是BIT\*的最主要步骤。或许你留意到了，我还没有叙述$$Q_E$$里面的边从哪里来。当然，想要增加边，树附近所有可能的边都是选项。但是为了更有针对性，算法希望能在边进入$$Q_E$$这一步有一定的筛选，也就是，筛选边的父节点，也就是树上的顶点。如Informed RRT\*节所述，通过$$x$$的路径的总代价为$$f(x)=g(x)+h(x)$$，对于一棵树，$$f_T(x)=g_T(x)+h(x)$$，下限$$\hat{f_T}(v)=g_T(v)+\hat h(v)$$，这也是以$$v$$为父节点的所有边$$e(v,x)$$的$$\hat{f_T}(e(v,x))$$的下限。记$$QueueValue(v)=\hat{f_T}(v)=g_T(v)+\hat h(v)$$，由树上所有顶点$$v$$和它们的$$QueueValue$$组成一个优先队列$$Q_V$$，按照$$QueueValue$$从小到大排列。

![queueValue(Qv)](http://lijiangfan.xyz/wp-content/uploads/2019/08/queueValueQv.png)

为了叙述方便，下面用$$BestQueueValue(Q)$$表示$$Q$$中最优的值，$$BestIn(Q)$$表示$$Q$$中最优的元素。

如果最优的$$BestQueueValue(Q_V)<BestQueueValue(Q_E)$$，这说明$$Q_V$$中的点对周围进行探索可能能找到比目前的$$BestIn(Q_E)$$估计更好的边。所以算法先进行探索，直到$$BestQueueValue(Q_V)>BestQueueValue(Q_E)$$。那么此时的$$BestIn(Q_E)$$就是树附近目前估计最优的边。

BIT\*不断循环，加入新的边，直到无边可用。或者$$Q_V$$和$$Q_E$$耗尽，或者$$BestIn(Q_E)$$也不能改善解。那么此时一个批次就结束了。下一个批次增加新的采样点，当然使用Informed采样，寻找更好的解。

### JIT sample

对于BIT\*这种Batch型的算法来说，可以使用Just-In-Time 采样，换句话说，随用随采。只有到需要附近的采样时，才对那一层椭圆环进行采样。

更具体地，以代码风格解释就是，用`costSampled`记录当前采样到的椭圆的长轴长，用`costReqd`表示当前的查询最近邻的顶点附近可能到的最大椭圆范围的长轴长。若`costReqd>costSampled`，需要对长轴长`(costSampled,costReqd]`的椭圆环进行采样；若`costReqd<costSampled`就不必采样，用之前的采样点就可以满足附近的采样需求。

![JIT1](http://lijiangfan.xyz/wp-content/uploads/2019/08/JIT1.png)

![JIT2](http://lijiangfan.xyz/wp-content/uploads/2019/08/JIT2.png)

如果使用JIT采样，那么采样点的数据结构应选择r最近邻（存储附近r内的点），而不是k最近邻（存储附近最近的k个点）。

## Referrence

[1] Jonathan D. Gammell, Batch Informed Trees (BIT\*): Sampling-based Optimal Planning via the Heuristically Guided Search of Implicit Random Geometric Graphs