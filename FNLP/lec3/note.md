# Model Evaluation
![[FNLP/lec3/lec4_pre.webp]]

[[lec4_pre.pdf#page=23&rect=7,54,358,199|lec4_pre, p.9]]
## K-折交叉验证
![[FNLP/lec3/lec4_pre 1.webp]]

[[lec4_pre.pdf#page=27&rect=16,25,358,176|lec4_pre, p.10]]

# N-gram Language Models
我们需要考虑一种方式，能够把位置信息也包含进去。
所以不能仅仅使用词袋的方式，而是需要使用一个Sequence模型。

### 马尔可夫假设
![[FNLP/lec3/lec4_lm.webp]]

[[lec4_lm.pdf#page=41&rect=27,104,322,199|lec4_lm, p.15]]

在很多语言中，语言的顺序并没有想象中那么的重要。

**目前可用的模型最大的只有7-gram**

当模型太大的时候，会出现越来越多的未见的sequence，造成统计数据上大量的零，以及运行速度大大减慢。

对于n-gram模型，其总大小为$O(|V|^n)$

然而绝大多数的序列其实都是0，所以并不是严格按照指数增长趋势，甚至比指数增长速度要慢很多。



> [!PDF|] [[lec4_lm.pdf#page=53&selection=117,0,117,31|lec4_lm, p.20]]
> > More data lead to better model?
> 
> 理论上如此。
> 

## 模型评估
> [!PDF|] [[lec4_lm.pdf#page=61&selection=11,0,11,51|lec4_lm, p.22]]
> > the best LM should best predict an unseen test set.

![[FNLP/lec3/lec4_lm 1.webp]]

[[lec4_lm.pdf#page=64&rect=108,54,275,84|lec4_lm, p.22]]
Perplexity越低，说明效果越好。
Perplexity在1和$|V|$之间，我们平时用的模型都是1.几。

# 处理未见过的情况
## 线性插值
把不同n-gram计算下的频率进行线性加权。
无法解决新词的问题。
只要保证$\lambda_1+\lambda_2+\lambda_3=1$，那么插值之后的概率分布仍然符合概率分布。

如何找到合适的$\lambda$值？（超参数）
- grid-search 不是一个好的办法

## stupid backoff![[FNLP/lec3/lec4_lm 2.webp]]

[[lec4_lm.pdf#page=88&rect=22,137,352,201|lec4_lm, p.31]]
这个看起来很扯，但是最终的效果还是不错的。
- 可能的解释，在大数据集上n-gram 和 （n-1)-gram存在线性关系

## smoothing
>有什么问题？

- 尤其在数据量过小的时候，可能会严重削弱那些已有观测数据的对应概率。
- 可能会出现一些在语法上不合理的结果，很多未见过是因为本身就不可能出现，而不是由于数据集的Bias而出现的。

## good-turing discounting
> [!PDF|] [[lec4_lm.pdf#page=98&selection=7,2,14,13|lec4_lm, p.35]]
> > Use the counts of n-grams that are seen once to help estimate the counts of unseen events

![[FNLP/lec3/lec4_lm 3.webp]]

[[lec4_lm.pdf#page=99&rect=21,111,291,182|lec4_lm, p.35]]
上面的推导，是基于两个出现次数相差1的情况没有太大的差别（定性）


自监督：不用标注数据了。

# 分类器
训练一个网络，利用Log-Linear模型进行判别式分类。

待解决的问题：
- 分类类别候选太多
- 如何代表$h_{1:i-1}$的特征？
- 如何优化这个模型？

# NLM1
![[FNLP/lec3/lec4_nlm.webp]]

[[lec4_nlm.pdf#page=28&rect=8,42,358,164|lec4_nlm, p.13]]
关键还是在于如何表示$w$

使用word embedding把$w$压缩到一个比较小的维度。
引入一个M矩阵，来存储dense 向量。

![[FNLP/lec3/lec4_nlm 1.webp]]

[[lec4_nlm.pdf#page=34&rect=8,137,283,224|lec4_nlm, p.15]]

![[FNLP/lec3/lec4_nlm 2.webp]]

[[lec4_nlm.pdf#page=36&rect=7,60,354,169|lec4_nlm, p.16]]
上面的A/T矩阵可以理解为带有一定的位置权重的位置编码矩阵。所以我们应该配有n个A，n个T。

>如何节省计算资源？


# NLM2

# NLM3

