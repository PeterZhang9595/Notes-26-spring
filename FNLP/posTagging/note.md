# POS
> [!PDF|] [[17.pdf#page=4&selection=474,0,485,81|17, p.4]]
> > Tagging is a disambiguation task; words are ambiguous —have more than oneambiguous possible part-of-speech—and the goal is to find the correct tag for the situation.

解决歧义。

## most frequent class baseline
为每个token分配在训练集中它出现频率最高的class。

# Named Entities and Named Entity Tagging
> [!PDF|] [[17.pdf#page=6&selection=63,0,70,50|17, p.6]]
> > A named entity is, roughly speaking, anything that can be referred to with anamed entity proper name: a person, a location, an organization

> [!PDF|] [[17.pdf#page=7&selection=21,0,27,76|17, p.7]]
> > Unlike part-of-speech tagging, where there is no segmentation problem since each word gets one tag, the task of named entity recognition is to find and label spans of text, and is difficult partly because of the ambiguity of segmentation; we need to decide what’s an entity and what isn’t, and where the boundaries are

## BIO tagging
用这个算法把NER转为了word-by-word的sequence labeling。
给每个entity class设置一个特定的B和I标签，所有class共用一个O。
O:outside of **any** span

# HMM 
把HMM应用于POS会是怎样的呢？（奇妙腔调）
这个话题我们已经接触过了，真正的问题在于，在POS中，我们的Observation是每个sequence里面的所有词汇，然后我们要做的是推测其hidden state，即词汇tag的类型，这条链是我们实际上研究的。
把HMM应用于POS问题，HMM tagger。

首先就是隐状态的转移概率。
然后就是隐状态的发射概率，都需要进行统计。

然后我们就把tagging问题转化为。
![[FNLP/posTagging/17.webp]]

[[17.pdf#page=11&rect=172,521,485,571|17, p.11]]

![[FNLP/posTagging/17 1.webp]]

[[17.pdf#page=11&rect=251,460,401,490|17, p.11]]
具体内容可直接参看第11页。
![[FNLP/posTagging/17 2.webp]]

[[17.pdf#page=11&rect=156,193,502,358|17, p.11]]

为了求解这个问题需要使用Viterbi算法
# CRF:conditional random fields
## linear chain CRF
在HMM中，我们没有显式计算我们想要优化的条件概率，而是利用贝叶斯公式进行变形。
但是在CRF中我们进行了显式的计算。
也就是说CRF是一个判别式的模型。

在每个token上，CRF不是直接预测出对应的tag，而是计算一系列相关特征，最终把局部特征整合为一个全局的概率。

![[Pasted image 20260416144623.png]]

所以关键在于对特征函数的设计。
- 固定搭配
- 0-1
- prefix\suffix
- word shape
- short word shape

![[FNLP/posTagging/17 3.webp]]

[[17.pdf#page=18&rect=141,456,484,530|17, p.18]]

## Inference and Training for CRFs
![[FNLP/posTagging/17 4.webp]]

[[17.pdf#page=19&rect=161,195,501,364|17, p.19]]
显然我们不可能使用穷举法列举所有的情况，因此我们依然使用Viterbi算法。
外层循环1-n来确定每个word对应的tag.
内层循环来对当前状态计算特征分数。

![[FNLP/posTagging/17 5.webp]]

[[17.pdf#page=20&rect=139,503,480,569|17, p.20]]

其中$w_k$是模型参数，是通过logistic regression实现的。

# Context-free grammars and constituency parsing
关键词
- context-free grammars
- CKY algorithm
- grammar checking
- parse trees

## constituency
词组的表现和single unit是相近的。

词组往往出现在相似的语义环境中。

## context-fress grammars
把语法建立在成分句法上。句子是由一个个嵌套的小单位逐级组合而成的。
![[Pasted image 20260417135236.png]]
CFG中符号分成两类
- terminal:直接对应某个单词
- non-terminal

每种grammar必须有一个对应的start symbol
![[FNLP/posTagging/18.webp]]

[[18.pdf#page=4&rect=138,391,483,617|18, p.4]]
![[FNLP/posTagging/18 1.webp]]

[[18.pdf#page=5&rect=156,488,498,686|18, p.5]]

## context-free grammar的正式定义
描述语法G
![[FNLP/posTagging/18 2.webp]]

[[18.pdf#page=6&rect=156,495,428,588|18, p.6]]

# Treebanks
可以根据treebank提取出CFG的语法规则。

# Grammar Equivalence and Normal Form
strongly equivalent 产生相同的语句集合以及为每个句子分配相同的分句结构
weakly equivalent 产生相同的语句集合但是为每个句子分配的分句结构不同

这段话出自 NLP 领域的圣经级教材 *Speech and Language Processing*（作者 Jurafsky & Martin）。

这段话在讲一个非常重要的概念：**乔姆斯基范式 (Chomsky Normal Form, CNF)**。

我给你拆解一下这段话的核心逻辑：

### 1. 什么是 Normal Form (范式)？
在语言学和计算机科学里，语法规则（Production rules）可以千奇百怪。为了让算法容易去处理语法，我们需要给语法“立规矩”，把所有随意的语法规则强行规整成固定的格式，这种固定的格式就叫 **Normal Form**。

### 2. 什么是 Chomsky Normal Form (CNF)？
乔姆斯基范式规定，一个上下文无关文法（CFG）要成为 CNF，它的**每一个生成规则（Production）只能是以下两种形式之一：**

* **形式一：$A \rightarrow B C$** 
   * $A, B, C$ 都是**非终结符 (Non-terminal)**（比如 NP, VP, S 这种短语标记）。
   * 意思是一个语法成分分裂成了**两个**语法成分。
* **形式二：$A \rightarrow a$**
   * $A$ 是**非终结符**，$a$ 是**终结符 (Terminal)**（具体的词，比如 "cat", "run"）。
   * 意思是一个语法成分直接指代了一个具体的词。

**除此之外，不允许出现任何其他形式！** 
比如像 $A \rightarrow B C D$ (3个非终结符) 或者 $A \rightarrow B$ 这种规则在 CNF 里是不合法的。

### 3. 为什么需要 CNF？(Binary Branching)
仔细看文中提到的 **Binary branching (二叉分支)**。

因为CNF的规则要么分裂成两个，要么指向一个词，所以最后画出来的成分句法树（Constituency Tree）**每一个节点最多只会有两个分支！** 

这对于算法来说是巨大的福音！假设一个语法规则后面跟着 5 个词，算法在处理的时候就需要写极度复杂的逻辑去递归。但如果是 Binary branching，算法只需要无脑的假设：*“我每次切分，都只切成两半”*。

### 4. 补充小知识：实际中如果遇到不符合 CNF 的规则怎么办？
在实际的 NLP 处理中，我们的语法树经常会有三个分支的情况，比如 $VP \rightarrow VBD \ NP \ PP$ （动词 + 名词短语 + 介词短语）。
遇到这种情况，编译器/解析器会使用**引入中间节点**的方法把它拆解成 CNF：
* 原规则：$VP \rightarrow VBD \ NP \ PP$
* 拆解后：
  * $VP \rightarrow VBD \ X$
  * $X \rightarrow NP \ PP$
  这样就把原本的三分支，转换成了两个符合 $A \rightarrow B C$ 的二分支规则了！

**最后一段话提到的 CKY parsing algorithm：**
这是 NLP 中一种非常经典的基于动态规划的句法分析算法，它就是利用了“所有的句子都可以被二叉化”这个特性，将句法分析的时间复杂度完美的控制在了 $O(n^3)$，这本书后面章节一定会讲到它，等你学到那个算法的时候，你就会彻彻底底明白为什么作者要在这里强调 CNF 了！

# Ambiguity
structural ambiguity 一套语法规则可以给同一个句子分配不同的分句

# CKY Parsing
## conversion to chomsky normal form
需要CNF作为基础语法。
把其它语法转化为CNF也比较简单。
- 如果terminal和non-terminal混合，那么就把terminal先转化为non-terminal，然后后续再对应到terminal
- 如果分叉超过两个，那么就把所有子节点进行均分抽象，逐步划分。

## CKY Recognition
这段文字描述的是自然语言处理（NLP）中非常著名的 **CKY 算法（Cocke-Kasami-Younger）** 的核心数据结构。

为了让你直观理解，我们把它拆解成四个核心概念：

### 1. 栅栏柱索引 (Fenceposts Indexing) —— 怎么数单词？
平常我们数单词可能是：第1个词、第2个词。但在 CKY 算法里，我们数的是**单词之间的缝隙**。

假设句子是：`Book that flight` (长度 $n=3$)
我们在单词之间插上“栅栏柱”：
**0** Book **1** that **2** flight **3**

*   位置 **0** 是句子的开头。
*   位置 **3** 是句子的结尾。
*   从 **0 到 2** 覆盖的是 `Book that`。
*   从 **0 到 3** 覆盖的是整个句子 `Book that flight`。

这就是为什么长度为 $n$ 的句子需要 $n+1$ 个索引（从 0 到 $n$）。

---

### 2. 二维矩阵 (The Matrix) —— 哪里存结果？
我们会建立一个 $(n+1) \times (n+1)$ 的表格。
*   **行 $i$** 代表起始位置。
*   **列 $j$** 代表结束位置。
*   **单元格 $[i, j]$** 存的是：**从位置 $i$ 到 $j$ 这一段词，能凑成哪些语法成分？**

比如：
*   单元格 $[0, 1]$ 存的是 `Book` 这个词对应的词性（如 Verb）。
*   单元格 $[1, 3]$ 存的是 `that flight` 对应的语法成分（如 NP，即名词短语）。
*   **最终目标：** 看单元格 **$[0, n]$**（即右上角）里有没有包含“S”（句子）这个符号。如果有，说明整个句子解析成功。

---

### 3. CNF 的分裂逻辑 (Split Point $k$) —— 怎么组合？
这段话最关键的一句是：*“每一个非终结符项都有两个女儿（daughters）…… 必然存在一个位置 $k$ 将其拆分为两部分。”*

在 **Chomsky 范式 (CNF)** 下，所有的语法规则只有两种：
1.  $A \rightarrow B \ C$ （一个大成分拆成两个小成分）
2.  $A \rightarrow \text{word}$ （一个词性对应一个具体单词）

这意味着，如果你在单元格 $[i, j]$ 里发现了一个成分 $A$，它一定是由于你在某个位置 $k$ 把这一段**切了一刀**，使得：
*   左半部分 $[i, k]$ 是 $B$
*   右半部分 $[k, j]$ 是 $C$
*   且 $i < k < j$

**例子：**
解析 `Book that flight` $[0, 3]$：
如果你认为整个句子是一个 $S$（句子），你需要找一个切分点 $k$：
*   如果 $k=1$：看 $[0, 1]$ (Book) 是否是 $V$，且 $[1, 3]$ (that flight) 是否是 $NP$。
*   如果符合规则 $S \rightarrow V \ NP$，那么 $[0, 3]$ 就可以填入 $S$。

---

### 4. 总结：动态规划的思想
这段话其实是在告诉你 CKY 算法的运作方式：

1.  **从小到大：** 先填长度为 1 的单元格（词性），再填长度为 2 的，最后填长度为 $n$ 的。
2.  **填表规则：** 为了填满单元格 $[i, j]$，你需要遍历中间所有的 $k$（从 $i+1$ 到 $j-1$），去查表看看能不能把 $[i, k]$ 和 $[k, j]$ 的成分组合起来。

> [!PDF|] [[18.pdf#page=13&selection=321,5,331,79|18, p.13]]
> > To do this, we’ll proceed in a bottom-up fashion so that at the point where we are filling any cell [i, j], the cells containing the parts that could contribute to this entry (i.e., the cells to the left and the cells below) have already been filled

关键就是从小到大，一点点划分，看可以凑成什么样的东西，直到能凑成一整个句子为止。
注意要尝试所有可能的拆分位置。
可以看图18.13很形象。

当然这套算法本质上是在验证一个句子是否可分，但更多时候我们需要找出最好的分句方案。
这里需要引入一套打分机制。

# Span-Based Neural Constituency Parsing
如何提供一个最好的解？
