
> **Artificial intelligence** (**AI**) is [intelligence](https://en.wikipedia.org/wiki/Intelligence "Intelligence")—perceiving, synthesizing, and inferring information—demonstrated by [machines](https://en.wikipedia.org/wiki/Machine "Machine"), as opposed to [intelligence displayed by humans](https://en.wikipedia.org/wiki/Human_intelligence "Human intelligence") or [by other animals](https://en.wikipedia.org/wiki/Animal_cognition "Animal cognition"). Example tasks in which this is done include speech recognition, computer vision, translation between (natural) languages, as well as other mappings of inputs.
> -- [Wikipedia](https://en.wikipedia.org/wiki/Artificial_intelligence)

## Structure

- [Artificial Intelligence](Artificial%20Intelligence.md)
	- [Major goals](1.%20Major%20goals/Major%20goals.md)
		- [Action](1.%20Major%20goals/Action/Action.md)
		- [Affection](1.%20Major%20goals/Affection/Affection.md)
		- [Intelligence](1.%20Major%20goals/Intelligence/Intelligence.md)
		- [Perception](1.%20Major%20goals/Perception/Perception.md)
	- [Approaches](2.%20Approaches/Approaches.md)
		- [Artificial neural network](2.%20Approaches/Artificial%20neural%20network/Artificial%20neural%20network.md)
	- [Applications](3.%20Applications/Applications.md)
		- [Computer graphics](3.%20Applications/Computer%20graphics/Computer%20graphics.md)
		- [Knowledge-intensive tasks](3.%20Applications/Knowledge-intensive%20tasks/Knowledge-intensive%20tasks.md)
		- [Medical Diagnosis](3.%20Applications/Medical%20Diagnosis/Medical%20Diagnosis.md)

The primary objective of artificial intelligence (AI) is to emulate human capabilities using artificial means. 

This note delineates the [major goals](1.%20Major%20goals/Major%20goals.md) of AI, the [approaches](2.%20Approaches/Approaches.md) used to achieve these goals, and the resulting [applications](3.%20Applications/Applications.md). It is important to differentiate between innate human abilities and acquired skills, as the former serves as the foundation for the latter, while the latter is an external manifestation of the former. For instance, [large language models](1.%20Major%20goals/Intelligence/Natural%20language%20processing/Large%20language%20model/Large%20language%20model.md) (LLMs) can perform recommendation tasks, which are not innate human abilities but acquired skills. Thus, pure AI should primarily focus on [major goals](1.%20Major%20goals/Major%20goals.md), with [approaches](2.%20Approaches/Approaches.md) essentially being the products that enterprises aim to implement.

[Major goals](1.%20Major%20goals/Major%20goals.md) encompass the human brain's intelligence, including abilities such as reasoning and memory. Additionally, humans possess [action](1.%20Major%20goals/Action/Action.md), [perception](1.%20Major%20goals/Perception/Perception.md) and [affection](1.%20Major%20goals/Affection/Affection.md). Controversial aspects like self-awareness and social ability can be considered as expressions of [intelligence](1.%20Major%20goals/Intelligence/Intelligence.md) to some extent, akin to the "emergent ability" often mentioned in the context of LLMs. However, this "emergent ability" is merely a superficial phenomenon, and it may be biased due to human evaluation methods. Lastly, the category of Perception has been largely addressed by electronic devices and neural networks, such as [speech recognition](1.%20Major%20goals/Perception/Speech%20recognition/Speech%20recognition.md) and [computer vision](1.%20Major%20goals/Perception/Computer%20vision/Computer%20vision.md).

Some overlap and ambiguity may arise in certain aspects. For instance, challenges arise when classifying [speech synthesis](1.%20Major%20goals/Action/Speech%20synthesis/Speech%20synthesis.md). Although it doesn't fit within the realm of perception by definition, assigning it to the intelligence category proves difficult, as simulated vocal cords aren't solely a result of intelligence. Thus, its inclusion in Natural Language Processing (NLP) may be a stretch. In this note, [speech synthesis](1.%20Major%20goals/Action/Speech%20synthesis/Speech%20synthesis.md) is assigned into [action](1.%20Major%20goals/Action/Action.md). Moreover, the boundaries between [intelligence](1.%20Major%20goals/Intelligence/Intelligence.md) and [perception](1.%20Major%20goals/Perception/Perception.md) become even more blurred when considering [computer vision (CV)](CV)). As CV encompasses an increasingly broad range of content, numerous areas now surpass the initial goal of simulating human capabilities. A notable example is 3D reconstruction, which already outperform humans. Consequently, it's challenging to determine whether CV belongs in [intelligence](1.%20Major%20goals/Intelligence/Intelligence.md) or [perception](1.%20Major%20goals/Perception/Perception.md). Further subdivisions might be necessary, such as categorizing 3D reconstruction under [computer graphics](3.%20Applications/Computer%20graphics/Computer%20graphics.md) – a field typically classified under [applications](3.%20Applications/Applications.md).


## AI vs. ML vs. DL

### AI

Artificial Intelligence (AI) is a branch of computer science that deals with the creation of intelligent machines that can perform tasks that typically require human intelligence, such as visual perception, speech recognition, decision-making, and language translation. The goal of AI is to create systems that can think and act like human beings, and to develop algorithms and models that enable computers to learn from experience and make decisions based on data.

笼统上AI就是终极目标，其中包括了CV、NLP、AGI、ML、[Robotics](https://en.wikipedia.org/wiki/Robotics)

### ML

Machine learning is called "machine learning" because it is a type of artificial intelligence (AI) that allows machines or computer systems to learn and improve on their own, without being explicitly programmed. The "learning" aspect of machine learning refers to the process of algorithms automatically improving their performance by analyzing data and recognizing patterns in that data. The algorithms continue to learn and make adjustments as they are fed more data, allowing them to make increasingly accurate predictions or decisions.

机器学习本质上是希望机器自主学习、不需要显示地设计（比如用if），而这一技能理论上也只是AI的一部分，比如CV可能就不需要机器学习就可以搞定。

There are several sub-areas of artificial intelligence (AI) that do not necessarily require machine learning. Here are a few examples:

1.  Rule-based systems: Rule-based systems are AI systems that make decisions based on a set of explicitly defined rules. They do not require machine learning, and instead use logic and reasoning to make decisions. Rule-based systems are often used in applications such as expert systems, where a human expert's knowledge is encoded in a set of rules.
2.  Planning and scheduling: Planning and scheduling systems use techniques from artificial intelligence and operations research to optimize the allocation of resources over time. They do not typically require machine learning, and instead use algorithms such as constraint programming, integer programming, and linear programming.
3.  Natural language generation: Natural language generation is the task of automatically generating human-like text from structured data. It can be accomplished using rule-based systems, template-based systems, or more advanced machine learning models, but not all natural language generation systems require machine learning.
4.  Robotics: Robotics is a subfield of AI that focuses on the design and development of robots and automated systems. While machine learning can be used in robotics to improve the performance of tasks such as object recognition and control, not all applications in robotics require machine learning.

In summary, while machine learning is a powerful tool in AI and is used in many applications, there are still many sub-areas of AI that do not require it. The choice between using machine learning and other approaches will depend on the specific problem and requirements of the application.


### DL

这范围就更窄了，就是ML下面地深度的模型。





