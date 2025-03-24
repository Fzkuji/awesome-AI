# Bradley–Terry model

The Bradley-Terry model is a probability model for the outcome of pairwise comparisons between items, teams, or objects.

## 问题举例

对比较关系进行建模：

| 对战    | 胜   | 负   |
| :---- | :-- | :-- |
| A 对 B | 8   | 4   |
| A 对 C | 3   | 5   |

**问题**：B战胜C的概率有多大？

**假设**：

每个人有一个隐含的实力参数，这个实力参数可以通过胜率来计算：
$$
P(i>j)=\frac{\alpha_i}{\alpha_i+\alpha_j}
$$
- $\alpha_i$ 表示第 i 个元素的实力。
- $P(i>j)$ 表示第 i 个元素战胜第 j 个元素的概率。

则 A 胜 B 的概率是：
$$
P(A>B)=\frac{\alpha_A}{\alpha_A+\alpha_B}
$$
B 胜 A 的楖率是：
$$
P(B>A)=\frac{\alpha_B}{\alpha_A+\alpha_B}
$$

A 胜 C 的檋率是：
$$
P(A>C)=\frac{\alpha_A}{\alpha_A+\alpha_C}
$$
C 胜 A 的概率是：
$$
P(C>A)=\frac{\alpha_C}{\alpha_A+\alpha_C}
$$

**分析**：

整个数据的联合概率（似然函数）就是所有比赛胜负结果的联合发生概率：
$$
L=\left(\frac{\alpha_A}{\alpha_A+\alpha_B}\right)^8 \times\left(\frac{\alpha_B}{\alpha_A+\alpha_B}\right)^4 \times\left(\frac{\alpha_A}{\alpha_A+\alpha_C}\right)^3 \times\left(\frac{\alpha_C}{\alpha_A+\alpha_C}\right)^5
$$
取对数则可以写出对数最大似然估计：
$$
\ln L=8 \ln \left(\frac{\alpha_A}{\alpha_A+\alpha_B}\right)+4 \ln \left(\frac{\alpha_B}{\alpha_A+\alpha_B}\right)+3 \ln \left(\frac{\alpha_A}{\alpha_A+\alpha_C}\right)+5 \ln \left(\frac{\alpha_C}{\alpha_A+\alpha_C}\right)
$$

为了最大化似然函数，我们对$\alpha_A$、$\alpha_B$、$\alpha_C$进行求导得到极值：
$$
\alpha_A=1 \quad \alpha_B=\frac{1}{2} \quad \alpha_C=\frac{5}{3}
$$
最后，B 胜 C 的概率是：
$$
P(B>C)=\frac{\alpha_B}{\alpha_B+\alpha_C} \approx 0.23
$$

## 应用

最大化似然函数可以加个符号，写成一般的Loss函数：

$$
\text { Loss }=-\mathbb{E}_{\left(\alpha_x, \alpha_y\right) \sim D}\left[\ln \frac{\alpha_x}{\alpha_x+\alpha_y}\right]
$$

强化学习里：
大模型输入的prompt是 x ，回答是 y 。
回答 y 的好坏（实力得分）是靠Reward模型来评估。

$$
P\left(y_1>y_2\right)=\frac{r\left(x, y_1\right)}{r\left(x, y_1\right)+r\left(x, y_2\right)}
$$

$r(x, y)$ 有可能返回负数，所以加上指数函数

$$
P\left(y_1>y_2\right)=\frac{\exp \left(r\left(x, y_1\right)\right)}{\exp \left(r\left(x, y_1\right)\right)+\exp \left(r\left(x, y_2\right)\right)}
$$

化简：
$$
P\left(y_w>y_l\right)=\frac{\exp \left(r\left(x, y_w\right)\right)}{\exp \left(r\left(x, y_w\right)\right)+\exp \left(r\left(x, y_l\right)\right)}
$$
$$
\sigma(x)=\frac{1}{1+\exp (-x)}
$$
$$
\begin{aligned}
\text { Loss } & =-\mathbb{E}_{\left(x, y_w, y_l\right) \sim D}\left[\ln \frac{\exp \left(r\left(x, y_w\right)\right)}{\exp \left(r\left(x, y_w\right)\right)+\exp \left(r\left(x, y_l\right)\right)}\right] \\
& =-\mathbb{E}_{\left(x, y_w, y_l\right) \sim D}\left[\ln \frac{1}{1+\exp \left(r\left(x, y_l\right)-r\left(x, y_w\right)\right)}\right] \\
& =-\mathbb{E}_{\left(x, y_w, y_l\right) \sim D}\left[\ln \sigma\left(r\left(x, y_w\right)-r\left(x, y_l\right)\right)\right]
\end{aligned}
$$
最终目标（这个我没检查，不知道对不对）：
$$
-\ln \sigma\left(r\left(x, y_w\right)-r\left(x, y_l\right)\right)
$$


