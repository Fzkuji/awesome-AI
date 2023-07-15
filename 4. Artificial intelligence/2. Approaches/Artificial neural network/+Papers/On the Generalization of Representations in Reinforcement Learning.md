## Link
Link: https://arxiv.org/abs/2203.00543

## Abstract
In reinforcement learning, state representations are used to tractably deal with large problem spaces. State representations serve both to approximate the value function with few parameters, but also to generalize to newly encountered states. Their features may be learned implicitly (as part of a neural network) or explicitly (for example, the successor representation of Dayan (1993)). While the approximation properties of representations are reasonably well-understood, a precise characterization of how and when these representations generalize is lacking. In this work, we address this gap and provide an informative bound on the generalization error arising from a specific state representation. This bound is based on the notion of effective dimension which measures the degree to which knowing the value at one state informs the value at other states. Our bound applies to any state representation and quantifies the natural tension between representations that generalize well and those that approximate well. We complement our theoretical results with an empirical survey of classic representation learning methods from the literature and results on the Arcade Learning Environment, and find that the generalization behaviour of learned representations is well-explained by their effective dimension.

状态表示是用于处理大型问题空间的。
状态表示可以用于：
 - 少量参数价值函数近似
 - 泛化到新遇到的状态
它们的特征可以被隐式（作为神经网络的一部分）或显式学习（后继表示）。
虽然表示的近似属性已经得到了相当好的理解，但缺乏对这些表示如何以及何时泛化的精确表征（也就是第二个）。
在这项工作中，我们解决了这一问题，并为由特定状态表示引起的泛化误差提供了一个信息边界。 此边界基于有效维度的概念，该概念衡量了知道一个状态价值的情况下，对其他状态的价值的了解程度。
该工作量化了近似和泛化的权衡，最后发现学习到的表示的泛化行为可以通过其有效维度得到很好的解释。