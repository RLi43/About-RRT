# 1.1 规划问题

本文中谈的路径规划器自然可以应用在各种情况中，不止是现实中真实存在的物理环境构成的地图$$map$$/空间$$space$$[^1]，也可以是抽象意义上的，比如一些人工智能问题的解法。本文使用这些规划器的主要目的是为机械臂规划轨迹。

[^1]: 插入英文是为了一些翻译的区分。比如 地图map 和 图graph 都可能被简称为“图”，以下将以英文作为定义（因为基本来源于Planning Algorithm这本书），并给出本书中使用的中文翻译。另外一些地方可能给出一些对应的名词。

## 问题定义

### state/状态

使用**状态**描述当前问题的特定情况，比如说一个六轴机器人的六个关节角的角度，一个无人机的空间坐标和空间朝向，汉诺塔的三个柱子上的分布……状态可以是离散的（如棋子）也可以是连续的（如无人车），可以是有限的也可以是无限的。使用合适的状态描述和范围有利于解决问题。比如要探讨清华大学中某个实验室的无人自行车的移动，就没有必要囊括纽约的实时气温作为状态描述。

一个问题的$$state\ space$$**状态空间**包括所有可能出现的状态。比如一个单位复数，若定义为$$a+bi$$，其状态由a,b描述，状态空间可以记为$$\mathcal{O}=\{(a,b)|a\in\mathbb{R},b\in\mathbb{R}, a^2+b^2=1\}$$。当然也可以将单位复数用$$e^{i\theta}$$表示，则状态由$$\theta$$描述，状态空间$$\mathcal{O}=\{\theta\in[0,2\pi)\}$$。

对于一般的机械臂，将会使用每个关节转动的角度为状态，各个关节角度构成的n维configuration space（将在第三节中叙述）作为状态空间。

下面将用$$x$$表示状态，$$\mathcal{O}$$表示状态空间。记不可行的状态构成的空间为$$\mathcal{O}_{obs}$$，可行为$$\mathcal{O}_{free}$$。比如一个二维格点$$[0,10]\times[0,10]$$的迷宫地图，用$$(a,b)$$表示当前所在的位置，迷宫的“墙壁”和界线构成$$\mathcal{O}_{obs}$$。

### Action/动作

动作表示状态之间的转换。动作应该明确定义如何使状态改变。再举二维格点迷宫的例子，动作“向右走”$$u_{right}$$定义为$$f(x(a,b),u_{right}) = x(a+1,b)$$。动作是否可行是与状态相关的，比如在迷宫的最右处向右走这一动作就不可行。下面将沿用$$u$$表示动作，$$f(x,u)$$表示状态转移函数。

### Plan/规划

一个规划问题可以定义为，给定起点$$x_I$$，终点$$x_G$$（当然也可以将他们定义为一个区域，比如$$X_G$$，方便起见，本文谈论的问题分别只有一个始末点，即$$X_I=\{x_I\},X_G=\{x_G\}$$），$$x_I,x_G\in\mathcal{O}_{free}$$，找到一系列动作使得$$x_I$$经过若干$$\mathcal{O}_{free}$$中的状态到达$$x_G$$。我们把这一系列的有序状态称为$$path$$路径。

规划一般有两类：

* 可行性规划 feasible planner
* 最优性规划 optimal planner

一般认为实现最优比找到可行要难，有些时候甚至连要优化的量都很难符号化表示。本书将先介绍RRT，再介绍实现渐进最优的RRT的一些改造版本。

关于规划的更多内容将在下一节离散规划中叙述。

## 一些例子

### 二维离散迷宫

这里用表格画了一个很简单的迷宫。start表示起点，goal表示终点，X表示障碍物。公式化的表示如下：

$$\mathcal{O}=\{(a,b)|a\in\{0,1,2\},b\in\{0,1,2\}\},$$

$$\mathcal{O}_{obs}=\{(0,1),(0,2),(2,0),(2,1)\},\mathcal{O}_{free}=\{(0,0),(1,0),(1,1),(1,2),(2,2)\}$$

$$x_{I} = (0,0),x_G=(2,2)$$

| X     |      | goal |
| ----- | ---- | ---- |
| X     |      | X    |
| start |      | X    |

规定只能向横纵相邻的格子移动，即上下左右移动。

显然$$path=\{(1,0),(1,1),(1,2)\}$$是一个解。

### 机械臂

在机器人轨迹领域，规划问题可以这样定义：给定始末位姿，求中间的路径点（位姿），使机械臂能够从起点开始不碰撞到达终点。

![constraint planning](http://openrave.org/docs/latest_stable/_images/constraintplanning.jpg)

在一些情况下要求机械臂满足一定的限制条件(constraint condition)，比如一个端水杯的任务就会限制机械臂末端（end-effector)握持水杯的姿势以保持水杯固定或近似竖直向上。

> TODO： 如何采样满足限制