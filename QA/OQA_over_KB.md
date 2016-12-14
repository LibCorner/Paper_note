# Open Question Answering Over Curated and Extracted Knowledge Bases

## 摘要
本文提出的OQA同时利用curated和extracted KBs.一个关键的技术挑战是如何对多变的自然语言问题和大量的KB具有鲁棒性。OQA通过把开放问答问题分解成更小的子问题，包括问题释义和查询重写。OQA通过从无标签的问题语料库和多个KBs中挖掘数十万的规则来解决这些子问题。然后，OQA学习通过在question-answer对上用因变量结构感知集算法训练来整合这些规则。我们在三个基准问题集上做了评测，结果表明我们的方法的精确率和召回率是state-of-the-art系统的两倍。

## 简介
Open-domain question answering（Open QA）已经有数十年的历史。Open QA需要广泛的知识来达到高覆盖率。早期的系统使用信息检索的方法，把问答简化为返回包含答案的文本段落。目前，大规模知识库的发展使得新的系统可以返回一个精确的答案。这样的系统有些是使用curated KBs，比如Freebase，使用curated KBs精确率很高但是并不完整。另外的一些系统是使用Open Information Extraction，这样的系统覆盖率高但是精确率低。本文提出了OQA系统同时利用curated 和extracted KBs.
Open QA的关键挑战是如何对变化率很高的自然语言和大规模知识库的多种只是表达方式具有鲁棒性。OQA通过把完整的QA问题分解成更小更容易解决的的子问题来获得鲁棒性。图1展示了OQA如何通过四步把问题"How can you tell if you have the flu?"映射为答案"chills"。首先，使用从大规模问题语料中挖掘的paraphrase operater把输入问题重写成"What are signs of the flu?"第二部使用，hand-written模板把paraphrased问题解析成KB查询"?x:(?x,signs of, the flu)。"这两个步骤相互增强；paraphrase operators有效的减少了输入问题的变化，允许OQA使用较小的高精确度的解析规则来维护召回率。第三步使用query-rewrite operator把查询重写为："?x:(the flu,sysmptoms,?x)". Query-rewrite operator是自动从KB中挖掘的，并允许问题单词和KB符号的词汇不匹配。最后，第四步，在KB上执行重写的查询并返回最后的答案。
operator和KB都是有噪音的，因此有可能构造很多不同的操作序列（称为derivation(派生)），这些derivations中很少的一部分可以产生正确的答案。OQA通过学习问答对数据来找到最好的derivation。因为derivations在训练数据中是不可见的，我们使用隐变量结构感知器算法。
综上所述，我们的贡献主要为：
* 我们介绍了OQA, 第一个利用多个大规模curated和extracted KBs的Open QA系统。
* 我们描述了一个推理算法来生成高可信度的答案（Section 6）和一个隐变量结构感知机算法来从数据中学习评分函数（Section 7）。
* 我们提出了自动从问题语料库中挖掘paraphrase operator和KB-query rewrite operator的算法（Section 8.2）。
* 我们提供了经验评估（Section 9）,展示了不同KB和不同系统组件的作用。我们还比较了OQA与state-of-the-art QA系统 PARALEX和SEMPRE。