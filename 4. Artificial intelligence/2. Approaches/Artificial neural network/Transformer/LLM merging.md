




## Related works


### TIES

![](../../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Transformer/LLM%20merging/IMG-20241217142842032.png)

- Trim
- Elect
- Disjoint Merge

### DARE

![](../../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Transformer/LLM%20merging/IMG-20241217142842059.png)

随机删除部分delta参数（微调参数-预训练参数），比如减少90%变化的参数；然后再以期望性能不变。

但是实验证明损失很大。。。


### DELLA

![](../../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Transformer/LLM%20merging/IMG-20241217142842090.png)
1. 按照delta参数的变化幅度进行排序，变化越小的参数被删除的概率越大，删除后进行一次缩放
2. 将多个模型的delta参数加和，根据最后的正负决定该参数的正负
3. 将和第二步得到的正负相同的delta参数进行平均，然后再进行缩放

