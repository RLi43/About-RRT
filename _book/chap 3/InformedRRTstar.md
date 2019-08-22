# 3.2.1 Informed RRT*

Informed RRT\*在RRT\*找到一个解之后，利用这个解的cost排除不可能的采样点，从而提高采样的效率。

![Informed](http://lijiangfan.xyz/wp-content/uploads/2019/07/Informed-1024x331.png)

## Cost-to-come, Cost-to-go

对于空间中的一点$$x$$，记从起点到$$x$$的代价为$$g(x)$$/cost-to-come，若可行的路径是一棵树，那么记通过这棵树到达该点的代价为$$g_T(x)$$。记$$g(x)$$的下限为$$\hat{g}(x)$$，也就是起点到$$x$$的直线距离。

![cost-to-come](http://lijiangfan.xyz/wp-content/uploads/2019/08/cost-to-come.png)

同样的，记从$$x$$到终点的代价为$$h(x)$$/cost-to-go。对于前向搜索来说，$$h(x)$$的值不得而知。$$h(x)$$的下限$$\hat{h}(x)$$就是$$x$$到终点的距离。

![cost-to-go](http://lijiangfan.xyz/wp-content/uploads/2019/08/cost-to-go.png)

记通过$$x$$的路径的总代价为$$f(x)=g(x)+h(x)$$。那么通过$$x$$的路径的代价下限$$\hat{f}(x)=\hat{g}(x)+\hat{h}(x)$$。

如果我们已知一个解的代价为$$bestCost$$，那么对于所有$$\hat{f}(x)>bestCost$$的$$x$$，树上加入这些点是没有可能改善这个解的。所以我们可以只对$$\hat{f}(x)<bestCost$$的范围进行采样，去掉不必要的采样点。这个范围就是一个超椭圆，得到一个解之后，我们可以只在这个超椭圆中进行采样，使得算法更有效率。

> 这里利用$$\hat f(x)$$是否让你想到了前面提到的A\*算法？下面的BIT\*将继续延续类似A\*算法的思想。

参考文献中的论文里阐述了如何对这个超椭圆进行均匀采样，此处不展开。

## 伪代码



## Referrence

[1] Jonathan D. Gammell, Informed RRT\*: Optimal Sampling-based Path Planning Focused via Direct Sampling of an Admissible Ellipsoidal Heuristic