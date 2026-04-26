# 动点脑子，不要自我重复
dry for short

# 写注释
Javadoc
```java
/**
 * Compute the hailstone sequence.
 * See http://en.wikipedia.org/wiki/Collatz_conjecture#Statement_of_the_problem
 * @param n starting number of sequence; requires n > 0.
 * @return the hailstone sequence starting at n and ending with 1.
 *         For example, hailstone(3)=[3,10,5,16,8,4,2,1].
 */
public static List<Integer> hailstoneSequence(int n) {
    ...
}
```

不仅要说明代码思路、代码功能，
还得注明代码出处
也有一个用处是避免代码过期。
要给模糊的代码进行注释。

# Fail Fast
静态-动态-错误结果
因此尽量要让程序尽早检测出错误并且停下来，避免浪费后续的一系列计算资源。

# 避免magic numbers
除了0，1，2，其余任何数字都应该被表明其意义。
可以把同类型的数字整理到一个List里面，建立某种映射关系，提高可读性。

不要把手算的结果hard code进入代码。

# one purpose for each variable
- 不要复用函数的参数
- 不要复用变量，当你不再需要一个变量的时候，就不要再用它了

在java里，给parameters一个final状态是一个很好的想法，这样的话如果在代码中你修改了parameter，那么在静态编译的时候就可以检测出来。

![[Pasted image 20260410213742.png]]

# 不要用全局变量！

# methods应该返回结果而不是打印它们
应该单开一个method来实现打印功能





