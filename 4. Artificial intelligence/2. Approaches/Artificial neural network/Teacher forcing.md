# Teacher forcing

用于训练循环神经网络的一种技巧。

通常如果要预测序列，需要将已经生成的内容（t时刻）再次输入RNN中，然后RNN输出t+1时刻的预测结果。

但是这样的操作问题在于，如果t时刻的预测本身就是错的，那将会导致RNN后续预测结果越来越错。因此，Teacher forcing方法就是在训练阶段，直接输入t时刻正确的结果，让模型预测t+1时刻，以避免错误的累加影响模型性能。

https://en.wikipedia.org/wiki/Teacher_forcing

Teacher forcing是什么？
https://www.cnblogs.com/dangui/p/14690919.html