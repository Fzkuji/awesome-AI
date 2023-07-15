-   Title: Domain Adaptation for Time Series Under Feature and Label Shifts (特征与标签转移下的时间序列域适应)
-   Authors: Huan He, Owen Queen, Teddy Koker, Consuelo Cuevas, Theodoros Tsiligkaridis, Marinka Zitnik
-   Affiliation: Huan He and Owen Queen are affiliated with the Department of Computer Science, University of California, Los Angeles (加州大学洛杉矶分校计算机科学系). Teddy Koker, Consuelo Cuevas, and Theodoros Tsiligkaridis are affiliated with the San Diego Supercomputer Center, University of California, San Diego (加州大学圣迭戈分校超级计算机中心). Marinka Zitnik is affiliated with the Department of Biomedical Informatics, Harvard Medical School (哈佛医学院生物医学信息学系).
-   Keywords: domain adaptation, time series, feature shifts, label shifts, transfer learning
-   Urls: [https://arxiv.org/abs/2103.02708](https://arxiv.org/abs/2103.02708), Github: None

## Summary:

- Background:
	- 该论文介绍了一种名为RAINCOAT的新型模型，用于应对复杂时间序列的无监督领域适应问题，探讨了数据集漂移和标签分布不一致等因素对于传输性的影响。
- Past methods and problems:
	- 传统的无监督领域适应方法不能很好地处理时间序列领域的[[Feature shift|特征漂移]]和[[Label shift|标签漂移]]问题，存在局限性。
- Methodology
	- RAINCOAT方法使用时间和频率编码器来学习时间序列表示，提出了Sinkhorn分布作为源和目标特征对齐方法，同时提出了对齐-纠正程序用于处理全领域适应问题。
- Task and performance
	- 在五个时间序列数据集上进行的实验表明，RAINCOAT方法优于13个现有的领域适应方法，能够取得高达16.33%的性能提升，同时能够应对闭合领域和全领域适应的问题。

## Background:

- Subject and characteristics:
	- 本论文的研究对象为时间序列数据的领域适应问题，具有很高的实际应用价值。
- Historical development
	- 随着现代医疗技术的发展，来自不同医院的时间序列数据往往存在差异，因此提出时间序列的领域适应方法尤为必要。
- Past methods
	- 传统的无监督领域适应方法不能很好地处理时间序列领域的特征漂移和标签漂移问题，存在局限性。
- Past research shortcomings
	- 以往的时间序列领域适应方法对于多数情况下的识别问题不能有效解决，为此本论文提出了RAINCOAT方法。
- Current issues to address
	- 目前的时间序列数据领域适应方法在特征和标签漂移方面存在很多挑战，需要更加鲁棒的模型来解决这些问题。

## Methods:

- Study's theoretical basis
	- 该研究基于Sinkhorn分布理论，使用时间和频率编码器来学习时间序列表示，提出了一种对齐-纠正程序用于处理全领域适应问题，从而提高模型的性能。
- Article's technical route (step by step)
	- 首先，使用时间和频率编码器来学习时间序列表示；其次，使用Sinkhorn分布作为源和目标特征对齐方法；最后，提出一种对齐-纠正程序用于处理全领域适应问题，提高模型性能。

## Conclusion:

-   a. Work significance
    -   本研究提出了一种新型领域适应方法RAINCOAT，能够应对复杂时间序列的无监督领域适应问题，对解决时间序列领域的识别问题具有重要的实际应用价值。
-   b. Innovation, performance, and workload
    -   与传统方法相比，RAINCOAT模型在处理时间序列的特征和标签漂移方面表现更具优势，能够处理闭合领域和全领域适应的问题，并为未来的应用带来更好的性能和更少的工作量。
-   c. Research conclusions (list points)
    -   本论文提出了RAINCOAT方法，是第一个可以处理复杂时间序列的闭闭合领域和全领域适应问题的模型；
    -   RAINCOAT采用时间和频率编码器来解决特征漂移和标签漂移问题；
    -   使用Sinkhorn分布进行源和目标特征对齐；
    -   提出对齐-纠正程序来处理全领域适应问题；
    -   实验结果表明，RAINCOAT在时间序列的领域适应问题上表现优秀，能够对传统方法进行改进。