# Attention
> [!PDF|] [[8.pdf#page=4&selection=36,0,50,2|8, p.4]]
> > Attention is the mechanism in the transformer that weighs and combines the representations from appropriate other tokens in the context from layer k to build the representation for tokens in layer k + 1.

![[FNLP/chap8/8.webp]]

[[8.pdf#page=5&rect=246,317,415,352|8, p.5]]

> [!PDF|] [[8.pdf#page=6&selection=148,2,156,3|8, p.6]]
> > Furthermore, the result of a dot product can be an arbitrarily large (positive or negative) value, and exponentiating large values can lead to numerical issues and loss of gradients during training. To avoid this, we scale the dot product by a factor related to the size of the embeddings, via dividing by the square root of the dimensionality of the query and key vectors (dk). 

![[FNLP/chap8/8 1.webp]]

[[8.pdf#page=7&rect=154,400,497,690|8, p.7]]

# Transformer Blocks
## 残差连接
始终保持一个residual stream，即attention\feedward计算的只是变化值。
![[FNLP/chap8/8 2.webp]]

[[8.pdf#page=9&rect=152,192,497,394|8, p.9]]

## 前馈网络
先升维再降维

## Layer Norm
> [!PDF|] [[8.pdf#page=10&selection=83,15,92,36|8, p.10]]
> > This process, called layer norm (short for layer normalization), is onelayer norm of many forms of normalization that can be used to improve training performance in deep neural networks by keeping the values of a hidden layer in a range that facilitates gradient-based training.

layerNorm只针对一个embedding向量，在它的d维上进行处理。

# 并行运算
![[FNLP/chap8/8 3.webp]]

[[8.pdf#page=13&rect=94,65,500,353|8, p.13]]

## 多头注意力上的并行化
可以把X复制多份发给不同的注意力头矩阵。

# 关于输入
![[FNLP/chap8/8 4.webp]]

[[8.pdf#page=15&rect=157,111,501,218|8, p.15]]
![[FNLP/chap8/8 5.webp]]

[[8.pdf#page=16&rect=139,573,478,689|8, p.16]]

# The Language Modeling Head
> [!PDF|] [[8.pdf#page=17&selection=110,49,125,10|8, p.17]]
> > aking the output of the last token at the last layer (the d-dimensional output embedding of shape [1 × d]) and producing a probability distribution over words (from which we will choose one to generate).

![[FNLP/chap8/8 6.webp]]

[[8.pdf#page=18&rect=75,473,481,686|8, p.18]]
注意unembedding矩阵的选择。

![[FNLP/chap8/8 7.webp]]

[[8.pdf#page=19&rect=152,286,499,689|8, p.19]]

# 关于采样
## Top-K
从中选择K个较高的词汇抽样。

## top-p
![[FNLP/chap8/8 8.webp]]

[[8.pdf#page=20&rect=138,354,482,422|8, p.20]]

# 关于训练
> [!PDF|] [[8.pdf#page=20&selection=176,0,180,62|8, p.20]]
> > Large models are generally trained by filling the full context window (for example 4096 tokens for GPT4 or 8192 for Llama 3) with text. If documents are shorter than this, multiple documents are packed into the window with a special end-of-text token between them. The batch size for gradient descent is usually quite large (the largest GPT-3 model uses a batch size of 3.2 million tokens).
> 
> 总而言之用的batch-size非常非常大。但是相比于模型本身又算是正常。

# Dealing with Scale
## scaling laws
- 模型参数量
- 训练数据量
- 迭代次数

![[FNLP/chap8/8 9.webp]]

[[8.pdf#page=22&rect=141,570,485,667|8, p.22]]
关注的是Loss function。

## KV Cache
在模型推理的时候，由于我们需要一个一个词地生成，所以为了避免在预测下一个词的时候重复计算前$i-1$个单词对应的QKV，我们需要把它们缓存起来。

![[FNLP/chap8/8 10.webp]]

[[8.pdf#page=23&rect=153,535,499,692|8, p.23]]
我猜想由于窗口大小受限，这个缓存应该用一个类似于定长队列的方式进行维护。

## 参数微调
**parameter-efficient fine tuning** PEFT
在微调训练的时候只针对选定的一系列参数进行反向传播更新，冻结剩余参数，只参与梯度传播，不更新。
## LoRA Low Rank Adaptation
并不是分解原始的$W_0$，而是增加一条并行路径。
$h=W_0 x + BA x$
在fine-tuning训练的时候，计算损失函数对A和B的梯度，更新A和B。
最后训练结束后$W=W_0+BA$

# 可解释性
## in-context learning
promoting技术
要和pre-training区分开。

**induction head**
**Induction Head（归纳头）** 是大语言模型（Transformer）中一种非常关键的机制，被认为是模型实现**上下文学习（In-Context Learning, ICL）**的核心原理。

这个概念最初由 Anthropic 的研究团队（A Mathematical Framework for Transformer Circuits）提出。下面我用通俗易懂的方式为你解释它的原理。

---

### 1. 归纳头到底在做什么？

归纳头的本质是一个**“模式完成”**（Pattern Completion）器。它能识别并完成类似这样的序列：
**`[A][B] ... [A] -> [预测 B]`**

**例子：**
假设输入文本是：`“达芬奇画了蒙娜丽莎。很久以后，达芬奇……”`
归纳头的作用就是看到当前的 `“达芬奇”`，回想起前面出现过 `“达芬奇”` 后面跟着 `“画了”`，于是预测下一个词是 `“画了”`。

### 2. 工作原理：两层电路机制

归纳头通常不是由一个神经元或一个注意力头独立完成的，而是需要**两层注意力层**协作完成。

#### 第一步：之前的词头（Previous Token Head）
*   **位置：** 发生在模型的较浅层（比如第 L 层）。
*   **任务：** 每一个 Token 都会观察它**前一个** Token。
*   **结果：** 在残差层中，当前位置的信息里被注入了“上一个词是谁”的信息。
    *   比如序列 `[A][B]`，在位置 `B` 的特征向量里，现在包含了 `A` 的信息。我们可以理解为 `B` 贴上了一个标签：“我的前任是 `A`”。

#### 第二步：归纳头（Induction Head）
*   **位置：** 发生在模型的较深层（比如第 L+1 层或更深）。
*   **任务：** 寻找匹配并复制。它包含两个关键电路：
    1.  **QK 电路（寻找“同类”）：** 当前的 Token 是 `A`。归纳头会去历史记录中寻找：**“谁的‘前任’也是 `A`？”**
    2.  **OV 电路（复制“后继”）：** 找到了！在序列前面的位置，词语 `B` 的特征里刚好存有“前任是 `A`”的信息。于是，归纳头注意力会高度集中在 `B` 这个位置上，并将 `B` 的内容提取出来。
*   **预测：** 将 `B` 的信息传递到当前层，预测下一个词就是 `B`。

---

### 3. 公式化的理解

如果你熟悉 Transformer 的 $Q, K, V$ 矩阵，可以这样理解：

1.  **Query (Q)：** 代表当前的 Token（例如 `A`）。
2.  **Key (K)：** 代表历史 Token 携带的“前任信息”（例如 `B` 携带的 `A`）。
3.  **Match：** 当 $Q(A)$ 与 $K(B\_with\_A\_info)$ 匹配时，注意力分值变高。
4.  **Value (V)：** 提取 $B$ 的内容并输出。

---

### 4. 为什么归纳头如此重要？

1.  **上下文学习（ICL）的引擎：**
    归纳头让模型具备了“现学现卖”的能力。即使用户输入的是完全随机的符号或虚构的语言（如 `“Qwerty -> 123, Qwerty -> ？”`），只要规律出现过一次，归纳头就能准确预测出 `123`。

2.  **模型训练中的“相变”：**
    研究发现，在大模型训练过程中，Loss 曲线会在某个时刻突然下降，这个点通常伴随着归纳头的大量涌现。这意味着模型从单纯的“背概率”进化到了“理解模式”。

3.  **长文本处理能力：**
    归纳头是模型处理长文本、保持逻辑一致性的基础，它让模型能跨越成千上万个词建立精确的联系。

### 总结

**归纳头（Induction Head）就像是模型的一个“快捷复制插件”：它在历史中寻找与当前情况相似的场景，并直接把那个场景之后发生的事情搬过来。** 这是 Transformer 能够展现出强大智能和学习能力的关键微观机制之一。

## logit lens
中途截断一层，拿出计算好的词汇向量，直接使用Unembedding进行解码。
