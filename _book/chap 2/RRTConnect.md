# RRTConnect

双向RRT算法，也被称为`BiRRT`。

伪代码

```python
# 分别以起点和终点初始化一棵树
V_1 = {q_init}; E_1 = {}; G_1 = (V_1,E_1)
V_2 = {q_goal}; E_2 = {}; G_2 = (V_2,E_2)
while i < N do:
    # 第一棵树与RRT相同
    i++
    q_rand = Sample()
    q_nearest = Nearest(G_1, q_rand)
    q_new = ExpandVertex(q_nearest, q_rand)
    if ObstacleFree(q_nearest, q_new):
        V_1.add(q_new)
        E_1.add((q_nearest,q_new))
        # 第二棵树向着新生成的点生长
        q_nearest2 = Nearest(G_2, q_new)
        q_new2 = ExpandVertex(q_nearest, q_new)
        if ObstacleFree(q_nearest2, q_new2):
            V_1.add(q_new2)
            E_1.add((q_nearest2,q_new2))
            # 尝试连接两棵树
            do:
                q_new3 = ExpandVertex(q_new2,q_new)
                if ObstacleFree(q_new2, q_new3):
                    V_1.add(q_new3)
                    E_1.add((q_new2,q_new3))
                    q_new2 = q_new3
                else: break
            while not q_new2 == q_new
        # 两棵树相遇 -> 成功
        if q_new2 == q_new: return SUCCESS
    # 交换树以均衡两棵树的规模
    if |V_2| < |V_1|:
        Swap(G_1, G_2)
```

RRTConnect增加了从目标点开始的树。第一棵树的扩展与普通RRT相同，进行随机扩展；不同的是第二棵树以第一棵树的新扩展qnew为目标进行扩展，并不断向qnew扩展直到到遇到障碍物。循环此过程，直到两棵树相遇。

为了保持平衡，让规模小的树进行随机扩展。这里规模的定义可以是顶点数，也可以是两棵树总共花费的路径长度，或是其他的定义。

[TODO：这里有一张GIF]

##  高速

这种双向的RRT技术具有良好的搜索特性，比原始RRT算法的搜索速度、搜索效率有了显著提高，被广泛应用。

首先，RRTConnect算法较之前的算法在扩展的步长上更长，使得树的生长更快；其次，两棵树不断朝向对方交替扩展，而不是采用随机扩展的方式，特别当起始位姿和目标位姿处于约束区域时，两棵树可以通过朝向对方快速扩展而逃离各自的约束区域。

这种带有启发性的扩展使得树的扩展更加贪婪和明确，使得双树RRT算法较之单树RRT算法更加有效。