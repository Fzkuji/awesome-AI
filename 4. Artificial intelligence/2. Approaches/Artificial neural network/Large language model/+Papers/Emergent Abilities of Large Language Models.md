Title: Emergent Abilities of Large Language Models
https://arxiv.org/abs/2206.07682

本文中对LLM的emergent abilities的定义为：

An ability is emergent if it is not present in smaller models but is present in larger models.
在较小的模型中不出现，而在较大的模型中出现的能力，则可以称之为emergent

本文的目的不是去谈论是否存在一个scale就可以观察到emerge abilities，而是去讨论之前的工作中出现的emergent现象。

## Few-shot prompted tasks

本部分主要讨论在prompting范式下的emergent abilities, 该范式如下图所示：

![400](Resources/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/Pasted%20image%2020230511121355.png)

> Few-shot prompting的emergent主要体现为模型在没有达到一定规模前，得到的表现较为随机，在突破规模的临界点后，表现大幅度提升。如下图所示，在BIG-Bench上，GPT-3和LaMDA在未达到临界点时，模型的表现都是接近于零。而在GPT-3的规模突破2 · 10^22 training FLOPs (13B参数)，LaMDA的规模突破10^23 training FLOPs (68B参数)，模型的表现开始快速上升。
> ——[大模型的涌现能力](https://zhuanlan.zhihu.com/p/609339534)

![2206.07682_page-0004](Resources/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/2206.07682_page-0004.jpg)

## Augmented prompting strategies


> 除了few-shot prompting可以反映emergent abilities, 作者认为如果在某个任务上的某个手段,模型达到一定规模以前，使用该手段，相比于基线没有提升或者是有害的，那么可以将该手段看成emergent ability.
> 
> 作者在Multi-step reasoning(chain-of-thought prompting)、Instruction following(使用instructions描述任务，不使用few-shot exemplars)、Program execution、Model calibration(calibration需要模型去评估自己是否能正确回答某个问题)四个问题上进行了实验。
> 
> ——[大模型的涌现能力](https://zhuanlan.zhihu.com/p/609339534)

![2206.07682_page-0005](Resources/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/2206.07682_page-0005.jpg)

最终汇总结果：
![2206.07682_page-0006](Resources/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/2206.07682_page-0006.jpg)

## Discussion


观察： 
- 涌现能力无法预测
- 一个猜想是更大的模型可能还会涌现更多的能力
- 涌现是实际存在的情况

### Potential explanations of emergence

- 对于模型突破某个规模的临界值后，出现emergent abilities的现象。作者给出直观上的猜想：
	- 某个multi-step reasoning任务需要l个step的计算，那么可能需要模型主要需要O(l)层的数目
	- 更多的参数和更多的训练有助于模型记忆更多的知识
		- 比如在closed-book question-answering可能需要模型有更多的参数去记忆尝试知识。
- 用于衡量“涌现”能力的评估指标也很重要。仅仅使用最终的指标如acc等并不一定很好反映emergence.

### Beyond scaling

除了扩大模型参数，改进模型结构、提升训练数据质量和使用不同的目标函数都可能更快的实现“涌现”的能力

一旦发现了一个特定的“涌现”能力，那么在小模型上其实也可以实现

已经有工作发现，一些特定的数据集可以让小模型更快地“涌现”

### Another view of emergence

“涌现”能力不一定只和参数量进行比较，也可以用其他指标

比如MMLU上的“涌现”能力和模型对文本的困惑程度相关：
![2206.07682_page-0009](Resources/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Large%20language%20model/2206.07682_page-0009.jpg)
当然，仅限于这一种“涌现”能力，并不是所有“涌现”能力都和困惑度相关

### Sociological changes

总而言之，“涌现”能力并不一定是“涌现”出来的，也可能是线性增长的。但是目前的发现已经改变了目前NLP的研究方向

### Directions for future work

作者为未来研究大模型中的emergent abilitie提供了一些方向。

1.  **Further model scaling:** 继续增加模型的规模探究模型的表现的提升。
2.  **Improved model architectures and training**：从模型的结构和训练过程上提高模型的质量，从而使模型在较低的训练成本下就可以获得emergent abilities.
3.  **Data scaling:** 增大数据集的规模
4.  **Better techniques for and understanding of prompting：** 更好地发挥prompt在模型中的作用
5.  **Frontier tasks:** 仍然有些任务无法出现emerent abilities，这也是值得探究的。
6.  **Understanding emergence:** 关于emergent abilities为什么会在语言模型中发生仍然是未知的。














