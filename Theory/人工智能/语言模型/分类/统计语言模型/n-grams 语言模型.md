---
title: n-grams 语言模型
date: 2026-04-07
  - 理论
  - AI
type: guide
status: complete
---

## n-grams 语言模型

## 概述

`N-Gram`是一种基于统计语言模型的算法。它的基本思想是将文本里面的内容按照字节进行大小为N的滑动窗口操作，形成了长度是N的字节片段序列。

每一个字节片段称为gram，对所有gram的出现频度进行统计，并且按照事先设定好的阈值进行过滤，形成关键gram列表，也就是这个文本的向量特征空间，列表中的每一种gram就是一个特征向量维度。

该模型基于这样一种假设，**第N个词的出现只与前面N-1个词相关，而与其它任何词都不相关**，整句的概率就是各个词出现概率的乘积。这些概率可以通过直接从语料中统计N个词同时出现的次数得到。常用的是二元的[Bi-Gram](https://zhida.zhihu.com/search?content_id=5320991&content_type=Article&match_order=1&q=Bi-Gram&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NjU5MzkxNjcsInEiOiJCaS1HcmFtIiwiemhpZGFfc291cmNlIjoiZW50aXR5IiwiY29udGVudF9pZCI6NTMyMDk5MSwiY29udGVudF90eXBlIjoiQXJ0aWNsZSIsIm1hdGNoX29yZGVyIjoxLCJ6ZF90b2tlbiI6bnVsbH0.n4u3OM4mh2NsqMWqVLqO901VghZfPuR1_KvcQpWmW1Y&zhida_source=entity)和三元的[Tri-Gram](https://zhida.zhihu.com/search?content_id=5320991&content_type=Article&match_order=1&q=Tri-Gram&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NjU5MzkxNjcsInEiOiJUcmktR3JhbSIsInpoaWRhX3NvdXJjZSI6ImVudGl0eSIsImNvbnRlbnRfaWQiOjUzMjA5OTEsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.n1rItmw5TWwCUTI1aDgmH6E5KutvLy9ob-koN6NJoi0&zhida_source=entity)。

### 概率：**从联合概率到条件概率**

我们的目标是得到一个由 `m`个词元组成的任意序列（即一个包含 `m`个词元的句子）的概率：

$$
P(w_1,w_2,...,w_{i-1},w_i)
$$

第一步是利用链式法则（`chain rule`）将联合概率转换成条件概率的连乘形式：

$$
P(w_1,w_2,...,w_{i-1},w_i) = P(w_1)P(w_2|w_1)P(w_3|w_1,w_2)|P(w_4|w_1,w_2,w_3)...P(w_{i-1}|w_1,w_2,...,P(w_{i-2})
$$

### **马尔可夫假设（The Markov Assumption）**

目前，这仍然是一个比较棘手的问题，因为随着上下文的不断增加，我们构建的模型中将包含越来越多的参数。所以这里，我们采用一种称为 “马尔可夫假设” 的简化假设：**某个单词出现的概率不再依赖于全部上下文，而是取决于离它最近的 `n`个单词**。因此，我们得到：

$$
P(w_i|w_1,w_2,...,w_{i-1}) ≈ P(w_i|w_{i-n-1},...,w_{i-1})
$$

对于某个很小的 ：

- 当  `n == 1` 时，一个 unigram 模型：
    
    $$
    P(w_1,w_2,\dots,w_m)=\prod_{i=1}^{m}P(w_i)
    $$
    
    *在 unigram 模型中，我们假设一个句子出现的概率等于其中每个单词单独出现的概率的乘积，这意味着每个单词出现的概率之间相互独立，即我们并不关心每个单词的上下文。*
    
- 当 `n == 2` 时，一个 bigram 模型：
    
    $$
    P(w_1,w_2,\dots,w_m)=\prod_{i=1}^{m}P(w_i\mid w_{i-1})
    $$
    
    在 bigram 模型中，我们假设句子中每个单词出现的概率都和它前一个单词出现的概率有关。
    
- 当 `n == 3` 时，一个 trigram 模型：
    
    $$
    P(w_1,w_2,\dots,w_m)=\prod_{i=1}^{m}P(w_i\mid w_{i-2},w_{i-1})
    $$
    
    在 trigram 模型中，我们假设句子中每个单词出现的概率都和它前两个单词出现的概率有关。
    

### **最大似然估计**

**我们如何计算这些概率？**

非常简单，我们只需要一个大的用于训练的语料库（corpus），然后我们就可以根据语料库中各个单词的计数（counts），利用最大似然估计来估计该单词出现的概率：

- 对于 `unigram` 模型：
    
    $$
    P(w_i)=\dfrac{C(w_i)}{M}
    $$
    
    其中， $C$ 是一个计数函数， $C(w_i)$表示单词 $w_i$在语料库中出现的次数，$M$ 表示语料库中所有单词 tokens 的数量。
    
- 对于 `bigram` 模型：
    
    $$
    P(w_i\mid w_{i-1})=\dfrac{C(w_{i-1},w_i)}{C(w_{i-1})}
    $$
    
    其中， $C(w_{i-1},w_i)$表示单词 $w_{i-1}$和单词 $w_i$前后相邻一起出现的次数。
    
- 对于 `n-gram` 模型：
    
    $$
    P(w_i\mid w_{i-n+1},\dots,w_{i-1})=\dfrac{C(w_{i-n+1},\dots,w_i)}{C(w_{i-n+1},\dots,w_{i-1})}
    $$
    
    **同理，我们计算 n-gram 出现的次数，除以 (n-1)-gram（即上下文）出现的次数。**
    

### **Trigram 例子**

现在，让我们来看一个玩具例子，假设我们有一个只包含两个句子的语料库。

- **语料库**：
    
    $$
    \texttt{<s> <s> } \textit{no no no yes yes yes no}\texttt{ </s>}
    $$
    
    $$
    \texttt{<s> <s> } \textit{no no no yes yes yes no}\texttt{ </s>}
    $$
    
    可以看到，每个句子开头有两个起始标记，因为我们采用的是 trigram 模型。
    
- **我们希望知道下面的句子在一个 trigram 模型下的概率是多少？**
    
    $$
    \texttt{<s> <s> } \textit{yes no no yes}\texttt{ </s>}
    $$
    
    $$
    \begin{align}
    P(\text{sent} =\textit{yes no no yes}) &= P(\textit{yes}\mid \texttt{<s>},\texttt{<s>})\times P(\textit{no}\mid \texttt{<s>},\textit{yes})\times P(\textit{no}\mid \textit{yes},\textit{no})\\
    &\quad \times P(\textit{yes}\mid \textit{no},\textit{no}) \times P(\texttt{</s>} \mid \textit{no},\textit{yes})\\
    &= \dfrac{1}{2} \times 1 \times \dfrac{1}{2} \times \dfrac{2}{5} \times \dfrac{1}{2} \\
    &= 0.1
    \end{align}
    $$
    
    **说明**：
    
    1. 首先，我们对要计算的句子的概率按照 trigram 模型拆分成条件概率的连乘形式。
    2. 然后，对于等式右边的每一个条件概率项，按照 trigram 模型中的条件概率计算公式，分别统计 “当前单词连同它的上下文” 以及 “单独的上下文部分” 在语料库中出现的次数，并将两者相除，得到该项的计算结果。
        
        例如，对于上面等式右边第一个条件概率项，我们考虑句子中第一个单词 “`yes`” 及其相邻的 bigram 上下文 “`<s>,<s>`”：
        
        $$
        P(\textit{yes}\mid \texttt{<s>},\texttt{<s>})=\dfrac{C(\texttt{<s>},\texttt{<s>},\textit{yes})}{C(\texttt{<s>},\texttt{<s>})}=\dfrac{1}{2}
        $$
        
        可以看到，子序列 “`<s>,<s>,yes`” 在语料库中只出现过 1 次；而子序列 “`<s>,<s>`” 在语料库中一共出现了 2 次，所以第一个条件概率项的结果为 1/2。其余各条件概率项的计算方式同理，另外请注意，在计算第四个条件概率项时，bigram 上下文 “`no,no`” 在语料库中一共出现了 5 次。
        

### 优劣

1. `n-grams` **具备对未知文本的泛化能力。但是，这种泛化能力会随着 n 的增大而逐渐减弱。**在 n-grams 语言模型中，n 代表了拟合语料库的能力与对未知文本的泛化能力之间的权衡。当 n 过大时，语料库中难以找到与 n-gram 一模一样的词序列，可能出现大量“零概率”现象；在 n 过小时，n-gram 难以承载足够的语言信息，不足以反应语料库的特性。因此，在 n-grams 语言模型中，n 的值是影响性能的关键因素。
2. `数据稀疏问题严重`，词汇量大时，很多 n-gram 组合在训练语料中根本没出现，导致概率为 0
3. `难以捕捉长距离依赖`，由于只考虑 n 个词，无法理解句子中远距离词之间的关系
4. `存储需求大`，n 增大时，可能出现指数级增长的组合数量，存储和查询效率下降。

## 附录

[自然语言处理 03：N-gram 语言模型 - YEY 的博客 | YEY Blog](https://yey.world/2020/03/09/COMP90042-03/)