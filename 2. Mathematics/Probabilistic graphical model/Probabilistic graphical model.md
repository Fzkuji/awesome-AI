Probabilistic graphical model 概率图模型

## Introduction and Overview

### Overview and Motivation

Let's first understand what each of these words mean in the context of this framework: 

The model (模型) is a **declarative** representation of our understanding of the world. 
![600](../../Resources/2.%20Mathematics/Probabilistic%20graphical%20model/Pasted%20image%2020230805113547.png)
Declarative means it stands on its own. 

I think a model is like a class in programming. 
- a model/class make sense of it aside from any algorithm/implementation
- a model/class can have multiple algorithms/implementations

Besides, an algorithm can be constructed from a model by **domain experts** or **learning from data**.


The word **probabilistic** is in there because these models are designed to help us deal with large amounts of uncertainty. So uncertainty comes in many forms and for many different reasons.

![400](../../Resources/2.%20Mathematics/Probabilistic%20graphical%20model/Pasted%20image%2020230805164127.png)

Probability theory is a framework that allows us to deal with uncertainty in ways that are principled and that bring to bear important and valuable tools.

![400](../../Resources/2.%20Mathematics/Probabilistic%20graphical%20model/Pasted%20image%2020230805164754.png)

Finally, the word graphical.  The word **graphical** is here from the perspective of computer science, because probabilistic graphical models are a synthesis between ideas from probability theory in statistics and ideas from computer science. 

The idea here is to use the connections computer science, specifically that of graphs to allow us to represent systems that are very complicated that involved large numbers of variables.

![400](../../Resources/2.%20Mathematics/Probabilistic%20graphical%20model/Pasted%20image%2020230805165002.png)

So what are graphical models?
![400](../../Resources/2.%20Mathematics/Probabilistic%20graphical%20model/Pasted%20image%2020230807113303.png)
two main classes of probabilistic graphical models
- Bayesian networks (or called directed networks)
- Markov networks (or called undirected networks)

![400](../../Resources/2.%20Mathematics/Probabilistic%20graphical%20model/Pasted%20image%2020230807113921.png)
For sparse parameterization, Koller said:
> Because of the way in which the graph structure encodes the parameterization of the probability distribution, we can represent these high-dimensional probability distribution efficiently using a very small number of parameters. Which allows us though feasible elicitation by hand from an expert as well as automatically learning from data. And in both cases a reduction in the number of parameters is very valuable. 


Finally, what will be covered below/The overview:
![500](../../Resources/2.%20Mathematics/Probabilistic%20graphical%20model/Pasted%20image%2020230807114231.png)

### Distributions

Here are some concepts about distribution.

#### Joint distribution

![400](../../Resources/2.%20Mathematics/Probabilistic%20graphical%20model/Pasted%20image%2020230808191542.png)

#### Conditioning

One important thing that we can do is condition the probability distribution on a particular observation.
![400](../../Resources/2.%20Mathematics/Probabilistic%20graphical%20model/Pasted%20image%2020230808191722.png)

##### Reduction

This is an operation that's called reduction. I've taken the probability distribution, I've reduced away stuff that is not consistent with what I've observed.

![400](../../Resources/2.%20Mathematics/Probabilistic%20graphical%20model/Pasted%20image%2020230808192328.png)

##### Renormalization

After reduction, the new distribution is 

