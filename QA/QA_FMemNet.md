# Question Answering over Knowledge Base using Factual Memory Networks

## 摘要
最近在自动问答任务上，Memory Networks在复杂的推理和扩展性上表现出色，不仅仅限制在训练数据包含的主题范围。这篇论文介绍了Factual Memory Network,可以根据相关的facts从知识库抽取和推理问题的答案。我们的系统在同一个词向量空间生成问题和KB的分布式表示，抽取初始候选facts的一个子集，然后使用mutli-hop推理和提纯，尝试寻找到答案实体的路径。另外，我们也通过各种计算的启发来提高模型运行时的效率。

## 简介
开放领域的问答系统是一个由几十年历史的长期问题。早期的系统使用信息检索的方法，把问答简化为文本段落的检索。最近大规模知识库KB的发展使得新的系统可以直接返回确切的答案。
开放QA的一个关键的挑战是如何更加健壮的处理多变的自然语言和知识库中知识表达的多种方式。另一个挑战是把自然语言问题与结构化的知识库语义关联起来。这篇论文，我们提出了一个新的基于memory network（Bordes et al.,2015）的架构,可以使用（question,answer）对来端对端的训练，而不是使用（question,facts in KB）对形式的强监督训练。
本文主要贡献有两个方面：首先，我们介绍了factual memory networks,用来回答自然语言问题。我们在不同的基准数据集上评估了我们的系统。因为KBs往往很大，搜索所有的实体和路径的计算效率很低，我们的第二个目标是提高模型的效率并通过智能的选择扩展的节点提供更好的相关事实的覆盖率。

## 相关工作
基于知识库的QA方法可以分为三种：语义解析，信息检索和基于embedding的。
基于Embedding的方法学习词和知识库的低维向量表示，然后使用这些向量的和来表示问题和候选答案。然而，简单的向量相加忽略了词的顺序信息。

## 处理知识库和问题
### 3.1 处理FreeBase
__FreeBase:__ Freebase(Bollacker et al.,2008)是一个巨大的，免费的事实数据库，组织成三元组的形式（subject Entity,Relationship,object Entity）。Freebase的实体和关系都有类别，并且类别和关系的词典类似。每个实体有一个内部的id和一些可选的名字集（别名）来在文本中引用该实体。
Freebase的整体结构是一个超图，两个以上的实体可以通过一个n-ary(n元)事实联系在一起。潜在的三元组存储使用虚拟(dummy)实体来表示这些事实，从而使真实的实体通过长度为2而不是1的路径连接。比如，对于表述“A starred as character B in movie C”在Freebase里表示成(A,'staring',dummy entity),(dummy entity,'movie',C)，(dummy entity,'character',B), dummy entity在这三个fact里有相同的内部id。
为了得到实体间的直接的链接，我们通过删除虚拟实体来修改这些facts，并使用第二个关系作为新的condensed(浓缩的) facts。比如前面的例子可以浓缩为：（A,'character',B）和（A,'movie',C）。同时，我们把这些浓缩的实体相互标记为另一的子孙。这样，（A,'character',B）是（A,movie,C）的子孙，反之亦然。进一步的，我们使用词条'fact'来表示Freebase里的triplet, 这些词条'fact'只包含真正的实体（没有dummy实体）。
经过上面的处理后，我们把每个实体和关系表示成向量，每个实体/关系向量是有它的词的平均词向量表示的。对于实体，我们还使用它的别名的词向量来进行计算平均值。

## Model

### 4.1 事实抽取
首先，我们生成一个初始的事实列表（叫做候选事实），用来输入到我们的模型。我们通过匹配问题中可能的n-gram词与Freebase中的实体的别名来生成这个列表。所有的匹配的facts加入到候选fact列表里。

###4.2.1 计算层
一个计算层接受两个输入：（1）一个fact列表(memory)F={f1...fn},每个事实f的形式为(s,R,o),s和o是实体向量，R是关系向量。（2）一个问题embedding。对于每个事实f，我们计算一个unnormalised的分数g(f)和normalised的分数h(f).
我们把事实f=（s,R,o）看作是一个问答对，(s,R)形成一个问题，o是答案。这样，g(f)计算给定的问题embedding和假设的问题q_=(s+R)的相似度。