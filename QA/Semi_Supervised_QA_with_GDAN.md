<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>
#Semi-Supervised QA with Generative Domain-Adaptive Nets


## 摘要
本文研究了半监督的问答问题，使用未标记的文本来提升问答的性能。本文提出了一个Generatie Domain-Adaptive Net（生成式领域适应网络）。这个框架中，使用生成模型来生成基于未标记文本的问题。为了减少模型生成的问题与人类生成的问题的分布差异，我们开发了一个新的基于强化学习领域适应算法。

## 简介

最近很多神经网络模型被应用到问答和阅读理解任务上，然而这些模型都依赖标注数据。收集大规模的问答数据是很困难的。 很多问答数据集都只有成千的问答对，比如WebQuestions, MCTest, WikiQA,和TREC-QA。 也有一些问答对收集了数十万的数据，包括SQuAD, MSMARCO 和NewsQA， 但是数据收集的过程很昂贵并且很耗时。这限制了问答系统在特定领域的实际应用。

与获取标注的问答对相比，获取未标记的文本数据相对容易。本文我们研究了半监督问答的问题： 是否可以利用为标注数据来提升问答系统的性能，尤其是当只有很小的标注数据可用时？ 这个问题很有挑战性，因为传统的manifold-based semi-supervised 学习算法不能直接应用。另外，由于多数问答任务的焦点是抽取，而不是生成，使用未标记数据来提升语言模型没有用在机器翻译中合理。

为了更好的利用未标记数据，我们提出了一个新的神经网络模型叫做Generative Domain-Adaptive Nets(GDANs). 首先，该框架使用语言标签（词性,命名实体等）从未标注的文本中抽取答案组块和他们的上下文。然后模型生成的问答对和人工生成的问答对可以用来训练问答模型，下文我们称为discriminative model。然而，模型生成的数据分布于人工生成的数据分布不一致，从而导致次最优的discriminative model. 为了解决这个问题，我们提出了两个领域适应技术，把模型生成的数据分布看作一个不同的领域。首先，我们使用一个额外的领域标签标志一个问答对是模型生成还是人工生成的。我们让discriminative model适应领域标签，这样discriminative model就可以学习domain-specific和domain-invariant的表示。然后，我们应用一个强化学习算法来fine-tune生成模型，用对抗的方式最小化discriminative的损失。

另外，我们为半监督问答提出了一个简单而有效的baseline方法，虽然这个半监督的方法表现比我们的GDAN方法差， 但是它很容易实现并且可以当只限制标注数据可用时性能会显著提高。

我们在SQuAD数据集上做了实验，该数据集有各种各样的labeling rates和大量的未标注数据。实验结果表明我们的GDAN框架同时在监督学习setting和basline 方法上得到了提高， 包括adversrial domain adaptation(对抗领域适应) 和dual learning（对偶学习）. 具体的， 当使用8K未标记数据时，GDAN模型比监督学习setting提高了F1 score 9.87 点.

我们的主要贡献有四方面。首先，不同于之间的神经网络问答系统，我们研究了关键的、具有挑战性的问题，半监督问答。 第二，我们提出了Generative Domain-Adaptive Nets，在生成式模型中应用了基于增强学习算法的领域适应技术。第三， 我们介绍了一个简单而有效的baseline方法。第四， 我们展示了我们的框架有显著的提高。

## 2 Semi-Supervised Question Answering

给定标注数据$L$和未标注数据$U$，半监督问答的目标是学习问答模型$D$, 该模型可以capture概率分布$P(a|p,q)$。我们称这个问答模型$D$叫做discriminative model，与下面要介绍的generative model相反。

### 2.1 简单的Baseline

给定一个段落 $p=(p_1,p_2,...,p_T)$ 和一个问题 $a=(p_j,p_{j+1},...,p_{k-1},p_k)$，我们从段落中抽取 $(p_{j-W},p_{j-W+1},...,p_{j-1},p_{k+1},p_{k+w})$ 并把它作为问题，即把答案周围的文本作为问题。 $W$是窗口大小，实现中设置为5. 这些基于上下文的问答对与人工生成的问答对$L$结合起来训练discriminative model。 令人惊叹的是，当标注数据有限制时，这个简单的baseline 方法取得了显著的提升。

## Generative Domain-Adaptive Nets

GDANs分为两个模型： 鉴别模型(discriminative model)和生成模型(generative model)

### 3.1 Discriminative model

* 使用BiGRU网络生成段落的表示$H_p$和问题的表示$H_q$。
* 使用gated-attention（GA）机制把问题和段落表示结合起来: 
	1. $$\alpha = softmax(H_p^TH_q)$$ 
	2. $H^K=\alpha H_q*H_p$
	
### 3.2 Domain Adaption with Tags

模型生成的问题和人工生成的问题分布不一致，这样会导致训练出一个biased model. 为了解决这个问题， 本文把模型生成数据的分布和人工生成的数据分布看作是两个不同的数据领域，并把领域适应整合到discriminative model中。

具体的， 我们使用一个领域标签作为discriminative model的额外输入， 我们把领域标签append到问题和段落中。

### 3.2 Generative Model

生成式模型学习给定段落和答案生成一个问题的概率，$P(q|p,a)$。 我们的生成模型以sequence-to-sequence的model实现，并使用了copy机制。

生成模型有一个encoder和一个decoder组成。encoder是一个GRU,可以把输入编码成隐藏的状态$H$。我们注入了一个额外的zero/one（0/1） 特征到wrod embedding中，比如，如果一个单词出现在答案中，这个特征设置为1，否则设置为0.

decoder是另外一个GRU并加入了attention机制。 每个时刻，所有单词类别的生成概率使用一个copy机制定义：

$P_{overall}=g_tP_{vocab}+(1-g_t)P_{copy}$

其中，$g_t$是从vocabulary生成token的概率，$(1-g_t)$是从paragraph里复制一个token的概率。$g_t$可以根据当前的隐藏状态$h_t$计算：

$g_t=\sigmoid(w_g^Th_t)$

其中$\sigmoid$表示logistic函数，$w_g$表示模型参数。 生成概率$P_{vocab}$定义为在所有单词类别上的softmax概率，而复制概率$p_{copy}$定义为在paragraph中单词类别softmax概率。$P_{vocab}$和$P_{copy}$都是根据当前的隐藏状态$h_t$和attention结果计算的。