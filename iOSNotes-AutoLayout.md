## iOS-AutoLayout

The fundamental building block in Auto Layout is the **constraint**.

	* Constant value
	* Relation
	* Priority level

Constraints are cumulative, and do not override each other. If you have an existing constraint, setting another constraint of the same type does not override the previous one.

布局四个按钮从左到右分别是:  
对齐(Align),固定(Pin),解决自动布局问题(Resolve Auto Layout Issues)和重定义尺寸(Resizing Behavior)(改变已经添加的约束)

	Leading Edges - 左边对齐
	Trailing Edges - 右边对齐
	Top Edges - 顶部对齐
	Bottom Edges - 底部对齐
	Horizontal Centers - 水平中心对齐(X)
	Vertical Centers - 垂直中心对齐(Y)
	Baselines - 文本底线对齐

	Horizontal Center in Container - 对于父视图的水平中心对齐(X)
	Vertical Center in Container - 对于父视图的垂直中心对齐(Y)


	间隔设定
	Width - 视图的固定宽度
	Height - 视图的固定高度
	Equal Width - 多视图保持等宽
	Equal Height - 多视图保持等高

使用固定(Pin)的时候,可能会引起冲突,解决冲突办法:

	第一宽度固定,但外边距可变;
	第二是外边距固定,但宽度可变.

Update Frames:移动和重定义尺寸来匹配约束  
Update Constraints:用约束来匹配视图  

AutoLayout是一种基于约束的，描述性的布局系统  
约束添加到作用的view上,遵循以下规则:

	1. 对于两个同层级view之间的约束关系，添加到他们的父view上
	2. 对于两个不同层级view之间的约束关系，添加到他们最近的共同父view上
	3. 对于有层次关系的两个view之间的约束关系，添加到层次较高的父view上

容易出现的错误:

	1. Ambiguous Layout 布局不能确定,即给出的约束条件无法唯一确定一种布局,也就是约束条件不足,无法得到唯一的布局结果.这种情况一般添加一些必要的约束或者调整优先级可以解决
	2. Unsatisfiable Constraints 无法满足约束,问题来源是有约束条件互相冲突,因此无法同时满足,需要删掉一些约束

布局原则:

	1. 有限个限制条件
	2. 不要缺少,不要重复,不要冲突
	3. 先考虑固定大小,相对不变的参数

注意:

限制添加完成后,需要更新update frames来显示效果,红色的线和数字表示距离实际位置的偏差

总结:

	1. 先添加所有固定的限制
	2. 不需要手动添加所有限制,会自动补全. 比如A-B,A bot space to B 和 B top space to A 是一致的,只添加1个即可.
	3. 多个view之间的限对位置限制

	
高级:
约束就是一个线性关系:
y = m * x + c

First Item : 对应公式中的 y, 表示因变量
Relation : = 表示相等 : Less or Equal, Greater or Equal
Second Item : 对应公式中的 x, 表示自变量
Multiplier : 对应公式中的 m, 表示缩放比例系数
Constant : 对应公式中的 c, 表示偏移常量

y 相对于 x 发生变化, 也可以Reverse 使 x 相对于 y 变化.

提示:
在视图列表中control+drag拖拽视图到另一个视图可以对其添加约束


