Java中的String是不可变对象，如果你要在上面进行改动，必须要创建一个新的对象来接受新的结果。

但是很多时候如果我们需要在String上面大量进行改动操作，为了减少temp变量，我们需要StringBuilder。

## Risk of mutation
可变性的缺点到底在哪？
- 传递可变的对象：如果不能避免，那就一定要避免在代码中对传递的对象本身造成改变。
- 返回可变的对象：如果对象本身是可变的，那么后面拿着method返回的对象进行操作的时候也有可能会修改掉原来的对象。

不可变对象永远都不用被防御性复制。

这个东西看似无足轻重，实则仔细想想，有多少的Bug都是这么出现的！

### 避免aliasing
在方法内部创建一个可变对象，然后仅仅在这个方法里面增删改。
![[Pasted image 20260507162104.png]]

### 把mutation写到method的specification里面
我们默认method绝对不会改动Input参数

## 关于iterator
如果在迭代的过程中对迭代对象本身进行修改（无论是单个对象、还是增删修改），那么大概率要出问题的！（可以回想Project1的Problem4)

# Mutation and Contracts
可变对象会严重影响代码编写者对代码的更改，代码编码者之间必须要建立非常复杂的合约。


# Useful immutable types
```
Collections.unmodifiableList
Collections.unmodifiableSet
Collections.unmodifiableMap
```