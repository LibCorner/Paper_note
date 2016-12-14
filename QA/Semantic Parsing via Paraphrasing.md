# Semantic Parsing via Paraphrasing

## 个人总结
本文介绍了一种通过Paraphrasing进行语义解析的方法来把自然语言问题映射为逻辑形式,该方法主要分为两个步骤：
* 第一步是根据自然语言问题生成多个canonical utterance：首先使用一些模板把input utterance转换成可能的逻辑形式，然后使用一些规则把每个逻辑形式再转换成canonical utterance。
* 第二步使用了association model和Vector space model两个paraphrase model为每个候选的canonical utterance进行评分，找到最好的一个canonical utterance和其对应的逻辑形式。association model通过使用pharaphrase 语料库(Paralex)得到一个1.3 million的association的短语对，并使用两个pharaphrase之间共同出现的lemma、POS、以及WordNet的派生关系形成的association。Vector space model使用Word2Vec训练词向量并使用词向量的平均值作为句子的表示，计算input utterance 和 候选canonical utterance之间的paraphrase评分。

本文在WEBQuestions和Free917两个数据集上做了实验，知识库使用Freebase，用Virtuoso数据库引擎进行存储和查询，逻辑形式使用$\lambda$-DCS, 并通过把它转换成SPARQL来执行查询。
