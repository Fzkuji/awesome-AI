# Bradley–Terry model

The Bradley-Terry model is a probability model for the outcome of pairwise comparisons between items, teams, or objects.

## 问题举例

对比较关系进行建模：

| 对战    | 胜   | 负   |
| :---- | :-- | :-- |
| A 对 B | 8   | 4   |
| A 对 C | 3   | 5   |

问题：B战胜C的概率有多大？

假设每个人有一个隐含的实力参数，这个实力参数可以通过胜率来计算：
$$
P(i>j)=\frac{\alpha_i}{\alpha_i+\alpha_j}
$$
- $\alpha_i$ 表示第 i 个元素的实力。
- $P(i>j)$ 表示第 i 个元素战胜第 j 个元素的概率。

整个数据的联合概率（似然函数）就是所有比赛胜负结果的联合发生概率：
$$
L=\left(\frac{\alpha_A}{\alpha_A+\alpha_B}\right)^8 \times\left(\frac{\alpha_B}{\alpha_A+\alpha_B}\right)^4 \times\left(\frac{\alpha_A}{\alpha_A+\alpha_C}\right)^3 \times\left(\frac{\alpha_C}{\alpha_A+\alpha_C}\right)^5
$$
取对数则可以写出对数最大似然估计：
$$
\ln L=8 \ln \left(\frac{\alpha_A}{\alpha_A+\alpha_B}\right)+4 \ln \left(\frac{\alpha_B}{\alpha_A+\alpha_B}\right)+3 \ln \left(\frac{\alpha_A}{\alpha_A+\alpha_C}\right)+5 \ln \left(\frac{\alpha_C}{\alpha_A+\alpha_C}\right)
$$





