# First Defense: Make Bugs Impossible
- static checking
- dynamic checking

final关键字，只能保证
```java
final List<String> list = new ArrayList<>();   // 引用 list 是 final 的

// 下面这行会报错：不能把 list 指向另一个新对象
// list = new ArrayList<>();  

// 但是可以修改 list 所指向的 ArrayList 对象的内容
list.add("hello");   // 允许
list.add("world");   // 允许
list.clear();        // 允许
```

# 2:Localize Bugs
比如当输入不符合要求的时候，直接扔出Error中断程序。
```java
/**
 * @param x  requires x >= 0
 * @return approximation to square root of x
 */
public double sqrt(double x) { 
    if (! (x >= 0)) throw new AssertionError();
    ...
}
```

# Assertions
同时兼顾了注释，fail-early以及辅助定位bug的价值
- 参数要求
- 返回值要求，在一定程度上验证method结果是否正确

当然不能滥用assertion。
为什么呢这是因为断言的设计初衷是检测程序内部的逻辑错误，它被触发的时候会直接终止程序。但有的时候，由于外部网络问题，输入格式等等问题，不应该终止程序，而是应该让用户端调整环境和输入，或者系统自行按照默认情况处理。

# 进行Test
边test边写code，写一点测试一点

# Modularity and Encapsulation
高内聚低耦合的系统能够更好地定位错误。

encapsulation：在module周围建立一堵墙
- 使用public 和 private来限制访问。尽量让更多的方程是private，减少调用。
- variable scope: 尽可能让variable scope小。
```java
for (int i = 0; i < 100; ++i) {
    ...
    doSomeThings();
    ...
}
```
在需要的时候才声明变量，放在最内层的花括号。