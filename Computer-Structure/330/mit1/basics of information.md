![[Pasted image 20260330191001.png]]
![[Pasted image 20260330191155.png]]
![[Pasted image 20260330191402.png]]
![[Pasted image 20260330192036.png]]
- naive parity-check:使用XOR检测一串信息内的1的奇偶个数。但是这个的判断能力有限。

假设我们希望编码方式可以最多容忍E个传输error，那么我们的编码长度就是E+1


![[Pasted image 20260330193358.png]]


而如果我们希望传输过程中最多出现E-bit的错误，而可以把对应的编码转化为原始正确编码时，编码的长度就应该是2E+1.

![[Pasted image 20260330193414.png]]