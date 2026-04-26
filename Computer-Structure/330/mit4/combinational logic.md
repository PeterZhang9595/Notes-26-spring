![[Pasted image 20260401090813.png]]
尤其要注意NAND和NOR不具有结合律。

一些常见的逻辑门库设计理念：
inverting gate:速度块、占地面积大
non-inverting gate:速度慢、占地面积小


# 关于布尔表达式化简
![[Pasted image 20260401093840.png]]
尽管理论上布尔表达式化简程度越高越好，但事实并非如此。我们还需考虑平衡各个部分的延迟时间。

# 找逻辑表达式方法
- 先根据truth-table中的相邻项绘制K-map
- 圈出所有的implicant区域
- 针对每个implicant区域进行或求和，注意辨认dont-care的那些输入值

# 使用MUX构建布尔函数
![[Pasted image 20260401102147.png]]

# ROM
![[Pasted image 20260401103212.png]]
![[Pasted image 20260401103232.png]]
ROM本质上是一个巨大的、遍历式的物理映射。
比如一个n输入，m输出的ROM，它的电路内部是$2^n$行$m$列的电路矩阵。

如果我们想要修改ROM的功能（硬件层面），那么硬件不变，我们只需控制行和列之间的连接是否要通过一个NFET接地。
这在一定程度上体现了可编程性。




