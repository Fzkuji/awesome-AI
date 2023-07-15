An **information filtering system** is a system that removes [redundant](https://en.wikipedia.org/wiki/Redundancy_(information_theory) "Redundancy (information theory)") or unwanted [information](https://en.wikipedia.org/wiki/Information_technology "Information technology") from an information stream using (semi)automated or computerized methods prior to presentation to a human user. Its main goal is the management of the [information overload](https://en.wikipedia.org/wiki/Information_overload "Information overload") and increment of the [semantic](https://en.wikipedia.org/wiki/Semantic "Semantic") [signal-to-noise ratio](https://en.wikipedia.org/wiki/Signal-to-noise_ratio "Signal-to-noise ratio"). To do this the user's profile is compared to some reference characteristics. These characteristics may originate from the information item (the content-based approach) or the user's social environment (the [collaborative filtering](https://en.wikipedia.org/wiki/Collaborative_filtering "Collaborative filtering") approach).


## IF vs IR

信息过滤（IF）和信息检索（IR）虽然都是帮助用户找到他们需要的信息的技术，但它们在使用方式和目标上有一些关键的区别：

1. 用户的主动性：信息检索通常是用户主动的过程。用户提供一个查询，然后系统返回与这个查询最相关的信息或文档。例如，当你在搜索引擎中输入关键词时，你正在进行信息检索。相反，信息过滤通常是一个被动的过程。系统基于对用户的兴趣和行为的理解，自动过滤出不相关的信息，并推送相关的信息给用户。例如，当你浏览社交媒体的信息流或新闻推荐服务时，你正在经历信息过滤。

2. 时间性：信息检索通常是一次性的操作，用户提出查询，系统返回结果，然后操作结束。但是信息过滤是一个持续的过程，系统需要持续监视用户的行为，更新对用户兴趣的理解，并根据这些理解推送新的信息。

3. 个性化：信息过滤通常涉及到更深度的个性化。因为信息过滤需要理解每个用户的兴趣和行为，所以它需要更多的用户特定的数据。相比之下，虽然信息检索也可以包含个性化元素（例如，搜索引擎可能根据用户的搜索历史调整结果），但这通常不是其主要焦点。

尽管这两者有所不同，但是在实践中，许多系统会结合使用信息检索和信息过滤。例如，一个搜索引擎可能会使用信息检索技术来找到与查询相关的网页，然后使用信息过滤技术来根据用户的搜索历史和点击行为个性化结果。