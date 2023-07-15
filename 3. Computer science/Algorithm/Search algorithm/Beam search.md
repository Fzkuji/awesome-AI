In [computer science](https://en.wikipedia.org/wiki/Computer_science "Computer science"), **beam search** is a [heuristic](https://en.wikipedia.org/wiki/Heuristic_(computer_science) "Heuristic (computer science)") [search algorithm](https://en.wikipedia.org/wiki/Search_algorithm "Search algorithm") that explores a graph by expanding the most promising node in a limited set.

Beam search是对贪心策略一个改进。思路也很简单，就是稍微放宽一些考察的范围。在每一个时间步，不再只保留当前分数最高的**1**个输出，而是保留**num_beams**个。当num_beams=1时集束搜索就退化成了贪心搜索。

![[Beam search demonstration.png|500]]