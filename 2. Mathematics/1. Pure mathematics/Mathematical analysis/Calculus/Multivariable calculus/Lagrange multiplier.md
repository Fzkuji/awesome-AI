# Lagrange multiplier
拉格朗日乘数法

In [mathematical optimization](https://en.wikipedia.org/wiki/Mathematical_optimization "Mathematical optimization"), the **method of Lagrange multipliers** is a strategy for finding the local [maxima and minima](https://en.wikipedia.org/wiki/Maxima_and_minima "Maxima and minima") of a [function](https://en.wikipedia.org/wiki/Function_(mathematics) "Function (mathematics)") subject to [equality constraints](https://en.wikipedia.org/wiki/Constraint_(mathematics) "Constraint (mathematics)") (i.e., subject to the condition that one or more [equations](https://en.wikipedia.org/wiki/Equation "Equation") have to be satisfied exactly by the chosen values of the [variables](https://en.wikipedia.org/wiki/Variable_(mathematics) "Variable (mathematics)")).
此方法是用来求方程在约束条件下的局部最大/最小值

假设有一个二元方程$f(x,y)$其约束为$g(x,y)=c$，我们的目标是最大化 $f(x,y)$。

方法：
- 令$\mathcal{L}(x, y, \lambda)=f(x, y)+\lambda (g(x, y)-c)$
- 此时，方程变成了三元方程（多了一元），对该方程分别求$x,\lambda$的偏导，因为我们要求极值，因此偏导为0，即$\nabla[f(x, y)+\lambda(g(x, y)-c)]=0$。
- 一旦求出$\lambda$的值，将其套入$\mathcal{L}(x, y, \lambda)=f(x, y)+\lambda (g(x, y)-c)$，易求在无约束条件下的极值和对应的极值点。新方程的极值点和原方程一致。

本质上就是求解一个三元方程组，包含约束在内有三个等式。



