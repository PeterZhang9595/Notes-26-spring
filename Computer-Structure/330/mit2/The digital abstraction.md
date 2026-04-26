如何去表示bit?
![[Pasted image 20260330194124.png]]
R & C:resistance and capacitance


>为什么要放弃连续电压？
	因为受到噪音的影响太大了。
		而连续值对于error的容忍性低，我们可以回想上一节课我们说编码对于Error-bit的容忍度那一部分的内容，我们必须让可被编码的电压拥有对噪音较高的容忍能力。

为了解决这个问题，我们引入了digital abstraction
![[Pasted image 20260330195834.png]]
![[Pasted image 20260330195949.png]]

# combinational
- 每个circuit都是组合逻辑
- 没有环
- ![[Pasted image 20260330200836.png]]

**引入一个概念，$t_{pd}$即传输时间，就是输入发生变化之后，输出达到并且稳定在最终有效数字状态所需的最长时间**

书接前文，我们现在需要考虑的问题是在输出电压的时候，我们可能会因为噪音导致输出电压时候电压落入forbidden zone里面。这是我们不希望看到的。
所以我们引入noise margin。
![[Pasted image 20260330202531.png]]

# VTC
![[Pasted image 20260330203100.png]]
![[Pasted image 20260330203405.png]]
为了实现如上图的电路性质，我们需要找到对应的非线性器件。
