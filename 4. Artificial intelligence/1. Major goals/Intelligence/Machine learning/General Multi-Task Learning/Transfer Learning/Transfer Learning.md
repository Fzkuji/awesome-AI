## 1. Multi-Task Learning vs. Transfer Learning

|                                 Multi-Task Learning                                 |                                                            Transfer Learning                                                             |
|:-----------------------------------------------------------------------------------:|:----------------------------------------------------------------------------------------------------------------------------------------:|
|      Solve multiple tasks $\mathscr{T}_{1}, \cdots, \mathscr{T}_{T}$ at once.       | Solve target task $\mathscr{T}_{b}$ after solving source task $\mathscr{T}_{a}$ by transferring knowledge learned from $\mathscr{T}_{a}$ |
| $\min _{\theta} \sum_{i=1}^{T} \mathscr{L}_{i}\left(\theta, \mathscr{D}_{i}\right)$ |                                          <u>Key assumption</u>: Cannot access data $\mathscr{D}_{a}$ during transfer.                                          | 

Transfer learning is a valid solution to multi-task learning.
(but not vice versa)

Question: In what settings might transfer learning make sense?
什么是时候适合用迁移学习？
 - when $\mathscr{D}_{a}$ is very large (don’t want to retain & retrain on $\mathscr{D}_{a}$)
	一般是预训练的数据集$\mathscr{D}_{a}$很大的时候
 - when you don’t care about solving $\mathscr{T}_{a}$ & $\mathscr{T}_{b}$ simultaneously
	两个任务不需要同时解决的时候

**目前来看，迁移学习主要还是依靠大规模数据集的优势，更好的解决小数据集的问题。**
## 2. Transfer learning via fine-tuning

$$\phi \leftarrow \theta-\alpha \nabla_{\theta} \mathcal{L}\left(\theta, \mathcal{D}^{\operatorname{tr}}\right)$$
where $\theta$ is the pre-trained parameters, $\mathcal{D}^{\operatorname{tr}}$ is the training data from new task.

**Where do you get the pre-trained parameters?**
- ImageNet classification
- Models trained on large language corpora (BERT, LMs)
- Other unsupervised learning techniques
- Whatever large, diverse dataset you might have
*Pre-trained models open available online.*

**Some common practices:**
- Fine-tune with a smaller learning rate
- Smaller learning rate for earlier layers
- Freeze earlier layers, gradually unfreeze
- Reinitialize last layer
- Search over hyperparameters via cross-val
- Architecture choices maLer (e.g. ResNets)

![Universal Language Model Fine-Tuning for Text Classification](../../../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/General%20Multi-Task%20Learning/Transfer%20Learning/Transfer%20Learning/IMG-20240212100315866.png)
**Universal Language Model Fine-Tuning for Text Classification**. Howard, Ruder. ‘18

*Fine-tuning doesn’t work well with small target task datasets!*
