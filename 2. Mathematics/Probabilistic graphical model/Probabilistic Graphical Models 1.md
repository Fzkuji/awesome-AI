# [Probabilistic Graphical Models 1: Representation](https://www.coursera.org/learn/probabilistic-graphical-models?specialization=probabilistic-graphical-models)

## Introduction and Overview

### Overview and Motivation

Let's first understand what each of these words mean in the context of this framework: 

The model (模型) is a **declarative** representation of our understanding of the world. 
![600](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100314978.png)
Declarative means it stands on its own. 

I think a model is like a class in programming. 
- a model/class make sense of it aside from any algorithm/implementation
- a model/class can have multiple algorithms/implementations

Besides, an algorithm can be constructed from a model by **domain experts** or **learning from data**.


The word **probabilistic** is in there because these models are designed to help us deal with large amounts of uncertainty. So uncertainty comes in many forms and for many different reasons.

![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100314990.png)

Probability theory is a framework that allows us to deal with uncertainty in ways that are principled and that bring to bear important and valuable tools.

![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315015.png)

Finally, the word graphical.  The word **graphical** is here from the perspective of computer science, because probabilistic graphical models are a synthesis between ideas from probability theory in statistics and ideas from computer science. 

The idea here is to use the connections computer science, specifically that of graphs to allow us to represent systems that are very complicated that involved large numbers of variables.

![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315028.png)

So what are graphical models?
![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315042.png)
two main classes of probabilistic graphical models
- Bayesian networks (or called directed networks)
- Markov networks (or called undirected networks)

![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315055.png)
For sparse parameterization, Koller said:
> Because of the way in which the graph structure encodes the parameterization of the probability distribution, we can represent these high-dimensional probability distribution efficiently using a very small number of parameters. Which allows us though feasible elicitation by hand from an expert as well as automatically learning from data. And in both cases a reduction in the number of parameters is very valuable. 


Finally, what will be covered below/The overview:
![500](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315071.png)

### Distributions

Here are some concepts about distribution.

#### Joint distribution

![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315083.png)

#### Conditioning

One important thing that we can do is condition the probability distribution on a particular observation.
![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315092.png)

##### Reduction

This is an operation that's called reduction. I've taken the probability distribution, I've reduced away stuff that is not consistent with what I've observed.

![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315115.png)

##### Renormalization

After reduction, the new distribution is no longer normalized any more.

*Unnormalized measure*, an indication, the word measure indicates that it's a form of distribution but the fact that it's un-normalized means that it doesn't sum to one, it doesn't normalize to one.

So we need renormalization: 

![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315124.png)
#### Marginalization

If we want to marginalize $I$, we need to sum up all probabilities for every same value from $D$.

![600](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315133.png)
### Factors

A **factor** is like **function**, while the **scope** contains the **variables** this function can take. 

>[!note] My own understanding
>*In this course, factors are the following distribution tables themselves.*

![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315149.png)
#### Cases

**Joint distribution** is an factor.

I suppose $P$ is the factor and $I$, $D$, $G$ are the scopes.
![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315160.png)

**Unnormalized measure** is an factor.

$I$ and $D$ are the scopes. Since $G$ here is constant, it is not a scope.
![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315168.png)

**Conditional probability distribution** (CPD) is also a factor with some constraints
![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315181.png)

Not all factors are about probability. Here is a general factor:

![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315198.png)
where $A$ and $B$ are the scopes.

#### Operations

##### Factor product

Combine two factors with shared scopes.

![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315207.png)

##### Factor marginalization

Marginalize a factor on one scope.
![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315218.png)

##### Factor reduction

Choose one value of a scope to reduce the factor. Below we choose $c_1$ and leave two scopes, $A$ and $B$.

![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315233.png)

### Why factors?

- Factors are fundamental building blocks for defining distributions in high-dimensional spaces [Semantics & factorization](#Semantics%20&%20factorization)
- Factors have a set of basic operations for manipulating these probability distributions
![500](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315243.png)


## Bayesian network (Directed model)

### Bayesian network fundamentals

#### Semantics & factorization

Factorization is to break a whole model into multiple simple factors: 
![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315253.png)

![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315265.png)

> We're going to say that a distribution $P$ factorizes over graph $G$, means we can represent it over the graph $G$ if we can encode it using the chain rule for Bayesian networks. So the distribution factorizes over a graph $G$ if I can represent it in this way as a product of these conditional probabilities.

Noted that $P$ is a distribution while $G$ is a graph. I think we can say $G$ can be represented by factors and encoded using the chain rule for Bayesian networks (like the image above) so that we can say $P$ factorizes over $G$.

![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315276.png)


#### Definition

![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315287.png)

When Bayesian network is legal  distribution:
- $P\ge 0$
- $\sum P = 1$

e.g.
![400](../../Attachments/2.%20Mathematics/Probabilistic%20graphical%20model/Probabilistic%20Graphical%20Models%201/IMG-20240212100315303.png)

### Reasoning patterns



