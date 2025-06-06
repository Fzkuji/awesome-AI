# Reinforcement learning

个人觉得强化学习和监督学习没有本质区别，都是根据输入给出预测，只不过监督学习的预测通常叫标签，强化学习通常预测的是下一步的行为。

## 分类

- 基于模型的强化学习
	- 基于价值
		- 
	- 基于策略
		- PILCO (Probabilistic Inference for Learning Control)
- 无模型的强化学习
	- 基于价值
		- [Temporal difference learning](Temporal%20difference%20learning.md)（时序差分）
			- Q-learning
				- DQN
			- Sarsa
	- 基于策略
		- Policy Gradient
		- DDPG
		- PPO
- Both
	- Dyna-Q

### model-based 🆚 model-free

通常他们会说：

> 根据是否具有环境模型，强化学习算法分为两种：**基于模型的强化学习**（model-based reinforcement learning）和**无模型的强化学习**（model-free reinforcement learning）

说人话就是：如果我们能够完全了解决策环境的运行逻辑，我们就是基于模型的（比如打游戏），如果我们不能完全了解我们身处环境的运行逻辑，或者这个环境非常复杂（比如在现实中表白），那就是无模型的。

**如果要解决AGI，那肯定是无模型的，或者是让模型自己理解这个世界的运行逻辑。**

### value-based 🆚 policy-based

另外他们还会说：

> Q-learning、DQN 及 DQN 改进算法都是**基于价值**（value-based）的方法，其中 Q-learning 是处理有限状态的算法，而 DQN 可以用来解决连续状态的问题。在强化学习中，除了基于值函数的方法，还有一支非常经典的方法，那就是**基于策略**（policy-based）的方法。
> 
> 对比两者，基于值函数的方法主要是学习值函数，然后根据值函数导出一个策略，学习过程中并不存在一个显式的策略；而基于策略的方法则是直接显式地学习一个目标策略。

说人话就是，如果我们基于某个奖励来做决策，我们就是基于价值，如果我们基于对观察环境的理解来做决策，我们就是基于策略。

这似乎是一种感性和理性的区别。基于价值的方法类似于人身体调控，假如我们吃了好吃的，大脑会释放多巴胺，给予我们奖励。基于策略的方法类似于我们的工作，我们知道背后的整体逻辑，社会运行的规律，知道我们要工作才能赚到钱。基于此，我们才愿意努力工作。

**如果要解决AGI，似乎二者缺一不可。缺少核心的奖励就没有内驱力，缺少对事情的理解就无法达到高级的智能。**




