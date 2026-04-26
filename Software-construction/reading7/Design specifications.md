# Deterministic
面对歧义等情况的时候，要能反映出程序功能的确定性。
同一个输入，在specification的限制下应该生成唯一的合理的输出。

# Declarative 
不给出中间步骤的细节，而只是关注于最终结果和初始状态的关系。

# Stronger
原有的Specification是S1，要更新的Specification是S2，那么要求S2必须比S2要更强。
- S2的前提约束条件弱于S1
- S2的后置条件，即方法承诺实现的功能要强于S1
即无论如何，S2都能保证S1正常运行。

# Diagramming specifications
用韦恩图的方式去理解。

# Designing good specifications
## 连贯性
是否全程在表述一个单一的功能？

## informative
函数的返回值是否能够充分说明一些信息？
是否存在歧义？

## strong enough
能够处理足够多的情况

## weak enough
不能过于宽泛，尤其是对于特殊情况的处理

## use abstract types
使用抽象数据结构而不是具体的实现数据结构，比如写Map，而不是红黑树或者哈希表

# Precondition的使用
- 如果写的太多了，会导致调用者应接不暇，或者在调用端出现问题的时候无法报出错误。因此很多时候一些precondition会直接在implementation内部，扔出exception进行处理。
- 但是有的时候，为了method的一些效率，必须放弃一些低效的检查，让调用者自己保证自己的输入是合规的，因此这些话需要写入precondition。

# Public and private
不要设置过多的Public函数，以保证代码的ready for change。

# Static and instance methods
for future!



