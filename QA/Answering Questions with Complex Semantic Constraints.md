# Answering Questions with Complex Semantic Constraints on Open Knowledge Bases

## 笔记
传统的KBQA只能回答具有一个或多个二元关系的简单的问题，而无法回答具有复杂语义约束(介词或状语约束)的问题，比如What was the currency of Spain before 2002?

本文分析了两种知识库:curated KBs（e.g, Freebase,DBPedia）和open KBs(自动从web上抽取的n-tuple集)。由于基于curated KBs的问答系统不能生成具有复杂约束的查询，并且curated KBs是不完全的，有些问题无法回答。而open KBs具有n-tuple的assertion，包含更多的语义信息，可以回答具有复杂语义约束的问题。

本文提出的n-Tuple-Assertion based Question Answering(TAQA)系统包含两个关键的组成部分：语义解析和open KB查询。语义解析部分首先使用Wikianswers paraphrasing templates数据集(5 million templates)对问题进行重写，然后使用语法依赖树来解析问题.查询部分使用基于对齐的答案抽取方法，使用Apache Solr索引数据。