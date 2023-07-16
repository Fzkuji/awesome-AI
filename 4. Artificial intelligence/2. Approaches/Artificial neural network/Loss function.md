




## Examples

### Huber loss

In statistics, the Huber loss is a loss function used in robust regression, that is less sensitive to outliers in data than the squared error loss. A variant for classification is also sometimes used.

The Huber loss function describes the penalty incurred by an estimation procedure $f$. Huber (1964) defines the loss function piecewise by 

$$
L_\delta(a)= \begin{cases}\frac{1}{2} a^2 & \text { for }|a| \leq \delta \\ \delta \cdot\left(|a|-\frac{1}{2} \delta\right), & \text { otherwise }\end{cases}
$$
This function is quadratic for small values of $a$, and linear for large values, with equal values and slopes of the different sections at the two points where $|a|=\delta$. The variable $a$ often refers to the residuals, that is to the difference between the observed and predicted values $a=y-f(x)$, so the former can be expanded to
$$
L_\delta(y, f(x))= \begin{cases}\frac{1}{2}(y-f(x))^2 & \text { for }|y-f(x)| \leq \delta \\ \delta \cdot\left(|y-f(x)|-\frac{1}{2} \delta\right), & \text { otherwise }\end{cases}
$$
The Huber loss is the convolution of the absolute value function with the rectangular function, scaled and translated. Thus it "smoothens out" the former's corner at the origin.

### Normalized Discounted Cumulative Gain
^62ea0d

Best explain: 
- [](https://arize.com/blog-course/ndcg/#:~:text=Normalized%20Discounted%20Cumulative%20Gain%20(NDCG)%20is%20a%20measure%20of%20ranking,or%20other%20information%20retrieval%20system.)
- [NDCG - 知乎 (zhihu.com)](zhihu.com))

Normalized Discounted Cumulative Gain (NDCG) is a ranking metric used to evaluate the quality of the ranking results in information retrieval and recommendation systems. It measures the effectiveness of a search algorithm by calculating the sum of the discounted relevance scores of the top K items in the ranked list.

The relevance scores are discounted based on their position in the list. The formula for NDCG is as follows:
$$
N D C G @ K=\frac{D C G @ K}{I D C G @ K}
$$
where $K$ means top-k ranking.

### Mean Reciprocal Rank
^0fb470

Mean Reciprocal Rank (MRR)

平均倒数排名（Mean Reciprocal Rank,MRR），该指标反应的是我们找到的这些item是否摆在用户更明显的位置，强调位置关系，顺序性。公式如下，N表示推荐次数，$p_i$表示用户真实访问的item在推荐列表中的位置，如果没在推荐序列中，则p为无穷大，1/p为0。
$$MRR=\frac{1}{N}\sum_{i=1}^{N}{\frac{1}{p_i}}$$
举个栗子：加入我们去top-5，推荐[1,2,3,4,5]这5个item，然后测试数据中对应的访问的item为3，则1/p=1/3。












