> [!PDF|] [[5.pdf#page=1&selection=155,0,168,10|5, p.1]]
> > Findingrepresentation learning such self-supervised ways to learn representations of language, instead of creating representations by hand via feature engineering, is an important principle of modern NLP
> 
> 相比于手动的特征工程，通过深度学习的方式让机器找到对语言的表达方式。

## Lexical Semantics
> [!PDF|] [[5.pdf#page=3&selection=12,0,16,2|5, p.3]]
> > For example when one word has a sense whose meaning is identical to a sense of another word, or nearly identical, we say the two senses of those two words are synonyms. 

> [!PDF|] [[5.pdf#page=3&selection=82,0,87,6|5, p.3]]
> > While words don’t have many synonyms, most words do have lots of similar words.

> [!PDF|] [[5.pdf#page=3&selection=158,0,159,12|5, p.3]]
> > The meaning of two words can be related in ways other than similarity. 
> > > [!PDF|] [[5.pdf#page=3&selection=194,0,197,1|5, p.3]]
> > One common kind of relatedness between words is if they belong to the same semantic field.
> 
> 

> [!PDF|] [[5.pdf#page=4&selection=96,3,103,31|5, p.4]]
> > The wordconnotations connotation has different meanings in different fields, but here we use it to mean the aspects of a word’s meaning that are related to a writer or reader’s emotions, sentiment, opinions, or evaluations.

## Vector Semantics
> [!PDF|] [[5.pdf#page=5&selection=26,64,28,31|5, p.5]]
> > Their idea was that two words that occur in very similar distributions (whose neighboring words are similar) have similar meanings.
> 
> > [!PDF|] [[5.pdf#page=5&selection=93,0,95,25|5, p.5]]
> > The idea of vector semantics is to represent a word as a point in a multidimensional semantic space that is derived (in different ways we’ll see) from the distributions of word neighbors

> [!PDF|] [[5.pdf#page=6&selection=27,0,29,25|5, p.6]]
> > And as we’ll see, vector semantic models like the ones showed in Fig. 5.1 can be learned automatically from text without supervision.

## Simple count-based embeddings
最简单的想法，记录一个单词的邻接矩阵。
一般来说这个矩阵还是相对稀疏的。

## cosine for measuring similarity
最原始的方法：点乘
点乘再除以模长，就是cos角度值。

> [!PDF|] [[5.pdf#page=10&selection=81,0,83,40|5, p.10]]
> > Cosine similarity can be used to estimate word similarity, for tasks like finding word paraphrases, tracking changes in word meaning, or automatically discovering meanings of words in different corpora. 

## word2vec
> [!PDF|] [[5.pdf#page=10&selection=111,19,125,6|5, p.10]]
> > embeddings are short, with number of dimensions d ranging from 50-1000, rather than the much larger vocabulary size |V |

> [!PDF|] [[5.pdf#page=10&selection=138,0,139,81|5, p.10]]
> > It turns out that dense vectors work better in every NLP task than sparse vectors.

- 学到的权重更加集中
- 更容易捕捉到单词的相似性

skip-gram with negative sampling

word2vec 可能是静态的，也可以是动态的。
> [!PDF|] [[5.pdf#page=11&selection=51,0,72,2|5, p.11]]
> > The revolutionary intuition here is that we can just use running text as implicitly supervised training data for such a classifier; a word c that occurs near the target word apricot acts as gold ‘correct answer’ to the question “Is word c likely to show up near apricot?” This method, often called self-supervision, 

> [!PDF|] [[5.pdf#page=11&selection=82,72,86,39|5, p.11]]
> > First, word2vec simplifies the task (making it binary classification instead of word prediction). Second, word2vec simplifies the architecture (training a logistic regression classifier instead of a multi-layer neural network with hidden layers that demand more sophisticated training algorithms)


skip-gram:
1. 我们先给所有可能出现的词汇一个随机初始化的embedding向量。
2. 构建训练集：给定一些语句，把句子中某个单词以及和它邻近的单词作为正向样本，而与它不邻近的单词作为反向样本
3. 使用逻辑回归，训练一个模型预测两个单词是否相邻，然后使用反向传播对他们的embedding进行修改。
![[FNLP/chap5/5.webp]]

[[5.pdf#page=12&rect=224,247,397,320|5, p.12]]

Skip-gram actually stores two embeddings for each word, one for the word as a target, and one for the word considered as context. 

- **关注一个很重要的概念：负样本采样，这在训练模型的时候也很有用**
而在实操的时候，负样本采样还是有一些细节的，这里主要提到的是给unigram赋予一个带权重的分布
[[5.pdf#page=14&selection=50,0,58,18|5, p.14]]

>明确训练的目的
>- embedding使得与target word相似或者相邻的词汇相似度高
>- 降低与target word没有一起出现的词汇与target word 的相似度
>- 当然，在skip-gram算法中我们是通过一个词的邻居来反推出这个词的，因此两个词相似，不是说它们总是相邻出现，而是他们的邻居总是相似的。




![[FNLP/chap5/5 1.webp]]

[[5.pdf#page=15&rect=159,105,502,166|5, p.15]]

> [!PDF|] [[5.pdf#page=16&selection=86,0,86,73|5, p.16]]
> > “I see well in many dimensions as long as the dimensions are around two.”
> 
> 笑死我了，人类的可视化能力还是太弱了。


# Semantic properties of embeddings
> [!PDF|] [[5.pdf#page=17&selection=30,38,42,38|5, p.17]]
> > When the vectors are computed from short context windows, the most similar words to a target word w tend to be semantically similar words with the same parts of speech. When vectors are computed from long context windows, the highest cosine words to a target word w tend to be words that are topically related but not similar.
> 
> 长window：主题相关
> 短window：语法相关

# Bias and Embeddings
可能会放大偏见。

# 模型评估
