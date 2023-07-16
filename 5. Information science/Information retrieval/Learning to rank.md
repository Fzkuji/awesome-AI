**Learning to rank** or **machine-learned ranking** (**MLR**) is the application of [machine Learning](../../4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/Machine%20learning.md), typically [supervised](../../4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/Supervised%20learning/Supervised%20learning.md), [semi-supervised](https://en.wikipedia.org/wiki/Semi-supervised_learning "Semi-supervised learning") or [reinforcement learning](../../4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/Reinforcement%20learning/Reinforcement%20learning.md), in the construction of [ranking models](https://en.wikipedia.org/wiki/Ranking_function "Ranking function") for [information retrieval](Information%20retrieval.md) systems. 

[Training data](https://en.wikipedia.org/wiki/Training_data "Training data") consists of lists of items with some [partial order](https://en.wikipedia.org/wiki/Partial_order "Partial order") specified between items in each list. This order is typically induced by giving a numerical or ordinal score or a binary judgment (e.g. "relevant" or "not relevant") for each item. The goal of constructing the ranking model is to rank new, unseen lists in a similar way to rankings in the training data.

## Approaches

[Tie-Yan Liu](https://en.wikipedia.org/wiki/Tie-Yan_Liu "Tie-Yan Liu") of Microsoft Research Asia has analyzed existing algorithms for learning to rank problems in his book _Learning to Rank for Information Retrieval_. He categorized them into three groups by their input spaces, output spaces, hypothesis spaces (the core function of the model) and loss functions: the pointwise, pairwise, and listwise approach. 

In practice, listwise approaches often outperform pairwise approaches and pointwise approaches. This statement was further supported by a large scale experiment on the performance of different learning-to-rank methods on a large collection of benchmark data sets.

### Pointwise approach

In this case, it is assumed that each query-document pair in the training data has a numerical or ordinal score. Then the learning-to-rank problem can be approximated by a regression problem — given a single query-document pair, predict its score. Formally speaking, the pointwise approach aims at learning a function $f(x)$ predicting the real-value or ordinal score of a document $x$ using the loss function $L(f,x_{j},y_{j})$.

A number of existing [supervised](https://en.wikipedia.org/wiki/Supervised_learning "Supervised learning") machine learning algorithms can be readily used for this purpose. [Ordinal regression](https://en.wikipedia.org/wiki/Ordinal_regression "Ordinal regression") and [classification](https://en.wikipedia.org/wiki/Classification_(machine_learning) "Classification (machine learning)") algorithms can also be used in pointwise approach when they are used to predict the score of a single query-document pair, and it takes a small, finite number of values.

### Pairwise approach

In this case, the learning-to-rank problem is approximated by a classification problem — learning a [binary classifier](https://en.wikipedia.org/wiki/Binary_classifier "Binary classifier") $ h(x_{u},x_{v}) $ that can tell which document is better in a given pair of documents. The classifier shall take two images as its input and the goal is to minimize a loss function $ L(h;x_{u},x_{v},y_{u,v}) $. The loss function may reflect the average number of [](https://en.wikipedia.org/wiki/Permutation#Inversions%20%22Permutation%22) in ranking.

In many cases, the binary classifier $h(x_{u},x_{v})$ is implemented with a scoring function $f(x)$. As an example, RankNet adapts a probability model and defines $h(x_{u},x_{v})$ as the estimated probability of the document $x_{u}$ has higher quality than $x_{v}$:
$$P_{u,v}(f)={\text{CDF}}(f(x_{u})-f(x_{v})),$$
where ${\text{CDF}}(\cdot )$ is a [cumulative distribution function](https://en.wikipedia.org/wiki/Cumulative_distribution_function "Cumulative distribution function"), for example, the [](https://en.wikipedia.org/wiki/Logistic_distribution#Cumulative_distribution_function%20%22Logistic%20distribution%22), i.e. 
$${\text{CDF}}(x)={\frac {1}{1+\exp \left[-x\right]}}.$$

### Listwise approach

These algorithms try to directly optimize the value of one of the above evaluation measures, averaged over all queries in the training data. This is difficult because most evaluation measures are not continuous functions with respect to ranking model's parameters, and so continuous approximations or bounds on evaluation measures have to be used.

### List of methods

<iframe 
	height = 750
	width = 100% 
	padding = 0 0 
	margins = 0 0
	src="https://en.wikipedia.org/wiki/Learning_to_rank#List_of_methods">
</iframe>


















