# BM25

## Definition

In [information retrieval](https://en.wikipedia.org/wiki/Information_retrieval), **Okapi BM25** (_BM_ is an abbreviation of _best matching_) is a [ranking function](https://en.wikipedia.org/wiki/Ranking_function "Ranking function") used by [search engines](https://en.wikipedia.org/wiki/Search_engine "Search engine") to estimate the [relevance](https://en.wikipedia.org/wiki/Relevance_(information_retrieval) "Relevance (information retrieval)") of documents to a given search query. It is based on the [probabilistic retrieval framework](https://en.wikipedia.org/wiki/Probabilistic_relevance_model "Probabilistic relevance model") developed in the 1970s and 1980s by [Stephen E. Robertson](https://en.wikipedia.org/wiki/Stephen_E._Robertson "Stephen E. Robertson"), [Karen Spärck Jones](https://en.wikipedia.org/wiki/Karen_Sp%C3%A4rck_Jones "Karen Spärck Jones"), and others.

BM25是一种用来评价搜索词和文档之间**相关性**的算法，它是一种基于**概率检索模型**提出的算法。

The name of the actual ranking function is _BM25_. The fuller name, _Okapi BM25_, includes the name of the first system to use it, which was the Okapi information retrieval system, implemented at [London](https://en.wikipedia.org/wiki/London "London")'s [City University](https://en.wikipedia.org/wiki/City_University,_London "City University, London") in the 1980s and 1990s. BM25 and its newer variants, e.g. BM25F (a version of BM25 that can take document structure and anchor text into account), represent [TF-IDF](https://en.wikipedia.org/wiki/TF-IDF "TF-IDF")-like retrieval functions used in document retrieval.

## The ranking function

BM25 is a [bag-of-words](https://en.wikipedia.org/wiki/Bag_of_words_model "Bag of words model") retrieval function that ranks a set of documents based on the query terms appearing in each document, regardless of their proximity within the document. It is a family of scoring functions with slightly different components and parameters. One of the most prominent instantiations of the function is as follows.

Given a query $Q$, containing keywords $q_{1},...,q_{n}$, the BM25 score of a document $D$ is:
$${\text{score}}(D,Q)=\sum _{i=1}^{n}{\text{IDF}}(q_{i})\cdot {\frac {f(q_{i},D)\cdot (k_{1}+1)}{f(q_{i},D)+k_{1}\cdot \left(1-b+b\cdot {\frac {|D|}{\text{avgdl}}}\right)}}$$
where $f(q_{i},D)$ is the number of times that $q_{i}$ occurs in the document $D$, $|D|$ is the length of the document $D$ in words, and avgdl is the average document length in the text collection from which documents are drawn. $k_{1}$ and b are free parameters, usually chosen, in absence of an advanced optimization, as $k_{1}\in [1.2,2.0]$ and $b=0.75$. ${\text{IDF}}(q_{i})$ is the IDF ([inverse document frequency](https://en.wikipedia.org/wiki/Inverse_document_frequency "Inverse document frequency")) weight of the query term $q_{i}$. It is usually computed as:
$${\text{IDF}}(q_{i})=\ln \left({\frac {N-n(q_{i})+0.5}{n(q_{i})+0.5}}+1\right)$$
where $N$ is the total number of documents in the collection, and $n(q_{i})$ is the number of documents containing $q_{i}$.


再用简单的话来描述下BM25算法：我们有一个$query$和一批文档$D_s$，现在要计算$query$和每篇文档$D$之间的相关性分数，我们的做法是，先对$query$进行切分，得到单词$q_i$，然后单词的分数由3部分组成：
-   单词$q_i$和$D$之间的相关性
-   单词$q_i$和$query$之间的相关性
-   每个单词的权重
最后对于每个单词的分数我们做一个求和，就得到了query和文档之间的分数。














