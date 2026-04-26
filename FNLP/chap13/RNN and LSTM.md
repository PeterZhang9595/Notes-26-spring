# RNN
![[FNLP/chap13/13.webp]]

[[13.pdf#page=2&rect=140,161,483,324|13, p.2]]
![[FNLP/chap13/13 1.webp]]

[[13.pdf#page=4&rect=77,427,479,687|13, p.4]]

> [!PDF|] [[13.pdf#page=4&selection=80,0,95,14|13, p.4]]
> > In the first pass, we perform forward inference, computing ht , yt , accumulating the loss at each step in time, saving the value of the hidden layer at each step for use at the next time step. In the second pass, we process the sequence in reverse, computing the required gradients as we go, computing and saving the error term for use in the hidden layer for each step backward in time. 
> > 这里是一个二次的反向传播，第一次只进行inference过程，并且根据每个生成的$y_t$计算一个局部梯度；第二次从最后一个输出开始，逐渐回传梯度，更新参数
> > 当然现代的话我们可以直接参考13.4，它已经是一个前馈网络的形态了。
> 
> 


> [!PDF|] [[13.pdf#page=7&selection=192,3,206,2|13, p.7]]
> > hen we move to the next word, we ignore what the model predicted for the next word and instead use the correct word wt+1 along with the prior history encoded to estimate the probability of token wt+2. 
> 
> 忽略模型预测的错误结果，使用ground truth作为下一步预测的基准。


简单直接的回答是：**通常不是。**

在标准的 RNN 训练中，并不是每预测一个词就更新一次参数 $U$ 和 $W$，而是采用一种叫做 **BPTT（Backpropagation Through Time，随时间反向传播）** 的算法。

为了让你更清晰地理解，我们可以从以下几个层面来拆解：

### 1. 权重共享（Weight Sharing）是核心
RNN 的一个核心特点是：**在整个序列的所有时间步中，使用的 $U$、$W$ 和 $V$ 是同一套参数。**

*   $U$：输入层到隐藏层的权重。
*   $W$：隐藏层到隐藏层的权重（循环连接）。
*   $V$：隐藏层到输出层的权重。

因为参数是共享的，所以每个时间步产生的梯度都会对同一组参数产生贡献。

### 2. 标准的更新流程（BPTT）
在一个典型的训练迭代中，过程如下：

1.  **前向传播（Forward Pass）：** 整个序列（或一段固定长度的序列）从头到尾走一遍。每输入一个词，计算一个隐藏状态 $h_t$ 和一个预测输出 $y_t$。
2.  **计算损失（Loss Calculation）：** 计算整个序列（或该段）的总损失。通常是把每个时间步的损失 $L_t$ 累加起来：$L = \sum L_t$。
3.  **反向传播（Backward Pass - BPTT）：** 
    *   计算梯度时，从最后一个时间步往回倒推。
    *   由于 $W$ 和 $U$ 在每个时间步都参与了计算，所以**总梯度是所有时间步梯度的累加**。
    *   例如：$\frac{\partial L}{\partial W} = \sum_{t} \frac{\partial L_t}{\partial W}$。
4.  **更新参数（Optimizer Update）：** 在这一轮（通常是一个 Batch）反向传播结束后，使用累加后的总梯度，通过优化器（如 Adam 或 SGD）**统一更新一次** $U$、$W$ 和 $V$。

### 3. 为什么不“每预测一个词就更新一次”？
如果你每输入一个词就更新一遍参数，会存在以下问题：
*   **计算极其低效：** 深度学习框架（如 PyTorch/TensorFlow）利用矩阵运算的并行性。频繁更新参数会破坏并行计算，导致训练极慢。
*   **逻辑矛盾：** RNN 的目的是学习序列的上下文关系。如果在序列中间就更新了 $W$，那么序列后面部分看到的 $W$ 就和前面部分不一样了，这破坏了“权重共享”的假设。
*   **不稳定性：** 单个词产生的梯度波动很大，会导致模型难以收敛。

### 4. 特殊情况：Truncated BPTT（截断 BPTT）
虽然不是“每预测一个词”就更新，但如果序列非常长（比如一本书有几万个词），模型不可能等读完一整本书再更新。这时会用到**截断 BPTT**：

*   我们会规定一个长度（比如 $k=50$）。
*   模型先读 50 个词，计算这 50 个词的总梯度，更新一次 $U$ 和 $W$。
*   然后继续读下 50 个词，再更新一次。
*   这种方法在“长文本处理”和“在线学习”中很常见。

### 5. 在 Batch 训练中的实际情况
在实际的代码实现中（比如处理 NLP 任务时）：
1.  你会输入一个 **Batch** 的句子（比如 32 个句子）。
2.  每个句子内部进行 BPTT。
3.  **最终是在这 32 个句子全部计算完梯度并求平均后，才进行一次参数更新。**

### 总结
*   **不是**每读一个词更新一次。
*   **是**在处理完一个**序列（或序列片段）**，并将所有时间步的梯度**累加**后，再统一更新参数。
*   在现代深度学习中，通常是处理完一个 **Batch** 后才更新一次。

![[FNLP/chap13/13 2.webp]]

[[13.pdf#page=11&rect=153,399,498,604|13, p.11]]
使用RNN完成生成式任务

## stacked and bidirectional RNN architectures
![[FNLP/chap13/13 3.webp]]

[[13.pdf#page=12&rect=138,489,478,687|13, p.12]]
这个是多层RNN

然后关于双向RNN，关键在于把词汇序列正向和反向排一遍，然后分别用一个RNN模型进行训练得到不同的hidden layer向量，把同一个时间t的两个不同h向量聚合到一起。

# LSTM
RNN的主要问题是它的记忆功能本质上还是会较大程度受到离他近的词语的影响。

- RNN的hidden layer难以同时完成为当前位置提供前向信息以及更新并保存最新信息的双重功能。
- RNN反向传播过程中容易出现梯度消失。
因此我们需要一个能够记住较远的信息的网络架构。当然如果这个架构还有遗忘的功能就更好了。

> [!PDF|] [[13.pdf#page=14&selection=99,50,107,63|13, p.14]]
> >  LSTMs divide the con-long short-term memory text management problem into two subproblems: removing information no longer needed from the context, and adding information likely to be needed for later decision making. 

> [!PDF|] [[13.pdf#page=15&selection=16,43,20,78|13, p.15]]
> > The choice of the sigmoid as the activation function arises from its tendency to push its outputs to either 0 or 1. Combining this with a pointwise multiplication has an effect similar to that of a binary mask. Values in the layer being gated that align with values near 1 in the mask are passed through nearly unchanged; values corresponding to lower values are essentially erased.
> 
> 结合sigmoid function和element-wise multiplication来接近一个mask的效果，选择要记住什么，要忘记什么

![[FNLP/chap13/13 4.webp]]

[[13.pdf#page=16&rect=75,474,494,688|13, p.16]]

# Attention
> [!PDF|] [[13.pdf#page=22&selection=206,0,219,31|13, p.22]]
> > The attention mechanism is a solution to the bottleneck problem, a way ofattention mechanism allowing the decoder to get information from all the hidden states of the encoder, not just the last hidden state.

