# Addressing Complex and Subjective Product-Related Queries with Customer Reviews（使用顾客评论解决复杂的，主观的产品相关性查询）

## 总结：
对于一些与产品相关的查询，这些查询一般都很复杂，并且带有主观性，通常使用基于事实的知识库是难以回答的。这篇论文使用顾客的评论数据来解决这样的查询，用户评论数据不仅仅能够回答一个产品的好或坏，而且还包含很多的主观信息，用户的个人体验等。本文的目的就是利用已有的评论数据，实现自动回答用户的查询，找到与查询相关的评论，而不是在Q/A社区中提问。

## 摘要
当考虑网上产品和交易的时候，在线的评论往往是首先要考虑的。当评估一个潜在的购买时，我们可能会在大脑里有有一个具体的要查询疑问。 要回答这样的问题有两种方法：浏览大量的用户评论从而找到一个最相关的，或者直接把问题在社区中提问。
本文中，作者融合了这两种方法：给定一个大规模的关于产品的查询和这些查询的答案，我们希望自动学习一个评论是否与查询相关。我们使用混合专家系统框架把这个问题形式化为一个机器学习问题：这里每个评论是一个专家，它对特定查询的响应进行投票；同时我们学习一个相关性函数，相关的评论就是那些投票正确的评论。在测试时，这个学习到的相关性函数可以用来找出一个与新的查询相关的评论。我们在一个新的语料库上进行评估，这个语料库有1.4 million的问题和答案，以及13million的评论。