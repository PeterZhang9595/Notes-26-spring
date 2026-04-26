一个句子的语法结构由一系列单词间的有向连接组成。
> [!PDF|] [[19.pdf#page=1&selection=109,0,112,21|19, p.1]]
> > This fact that the head-dependent relations are a good proxy for the semantic relationship between predicates and their arguments is an important reason why dependency grammars are currently more common than constituency grammars in natural language processing.

能很好地代表谓词以及其论元之间的关系。

# Dependency Relations
head:中心组织词
dependent：修饰词
把head和直接dependent词连在一起。

把一个dependency structure视作一个有向图$G=(V,A)$

- 必须是有向无环图
- 只有一个根节点，没有入边
- 除了根节点，其他节点有且仅有一个入边
- 从root到任何其它节点都是联通的

## projectivity

## Dependency Treebanks

# Transition-Based Dependency Parsing
基于stack，每次只取栈头的两个进行判断。
- 当前词设定为某些曾经看过的词的head
- 把某些曾经看过的词设定为当前词的head
- 储存起来

- LeftArc:把dependent那个词扔出去
- RightArc
- Shift：把Input buffer的词推到stack里面

本质是贪心的算法。

## Creating an oracle
我们如何生成一个在栈顶进行选择的规则函数?
使用监督学习的方式。

### Generating Training Data
一般使用最简单的方式：一个reference list，进入内部进行查找

rigtharc:之前 Oracle 决策逻辑“buffer front 的所有从属词已分配”就转变为了**“栈顶词（即将成为从属词的那个）的所有从属词已经全部在栈内处理完毕”**

注意区分训练和推理：
训练的时候拿着完整的树，就可以生成正确的动作序列。
然后用一些嵌入的方式把状态（栈，缓冲区，已有的匹配）转为数字表示
用一个参数网络拟合，生成三种不同的结果之一
然后反向传播更新参数


# Evaluation
![[Pasted image 20260425115239.png]]

