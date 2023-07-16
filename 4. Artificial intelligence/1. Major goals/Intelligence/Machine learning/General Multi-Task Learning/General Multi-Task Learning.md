## Introduction

Deep learning allows us to handle unstructured inputs (pixels, language, sensor readings, etc.) without hand-engineering features, with less domain knowledge
深度学习使我们能够处理非结构化输入（像素、语言、传感器读数等），而无需手工设计功能，且领域知识较少。

多任务学习（[Multi-task Learning](Special%20Multi-Task%20Learning/Special%20Multi-Task%20Learning.md)）、元学习（[Meta Learning](Meta%20Learning/Meta%20Learning.md)）和迁移学习（[Transfer Learning](Transfer%20Learning/Transfer%20Learning.md)）都是机器学习的重要分支，它们的共性主要体现在以下几个方面：

1. **共享知识**：这三种学习方法都依赖于在多个任务或环境中共享知识。在多任务学习中，模型同时学习多个相关任务，共享表示以提高性能。在元学习中，模型学习如何从一个任务快速适应到另一个任务，这也需要在任务之间共享知识。在迁移学习中，模型将从一个任务中学到的知识应用到另一个任务中，这同样需要知识的共享。
2. **泛化能力**：这三种方法都强调了模型的泛化能力，即模型不仅要在训练任务上表现良好，还要能够在新的、未见过的任务或环境中表现良好。
3. **提高学习效率**：这三种方法都试图通过利用任务之间的相关性来提高学习效率。多任务学习通过同时学习多个任务来提高效率，元学习通过学习如何快速适应新任务来提高效率，迁移学习则通过将已有的知识应用到新任务中来提高效率。
4. **处理数据稀缺问题**：在许多实际应用中，对于某些特定任务，可能只有有限的标注数据。这三种方法都可以在一定程度上解决这个问题。多任务学习可以通过共享在其他任务上学到的知识来提高对稀缺任务的性能，元学习可以通过学习如何从少量样本中快速学习来处理数据稀缺问题，迁移学习则可以通过将在大数据任务上学到的知识迁移到数据稀缺的任务上来解决这个问题。

总的来说，这三种方法都试图通过在任务或环境之间共享知识，提高模型的泛化能力和学习效率，以及处理数据稀缺问题。因此本笔记将这三者都归为[广义多任务学习](.md)，而通常的多任务学习被命名为[狭义多任务学习](Special%20Multi-Task%20Learning/Special%20Multi-Task%20Learning.md)。

### 1. Why should we care about (deep) multi-task & meta-learning?

Q: *What if you don’t have a large dataset?*
A: Impractical to learn from scratch for each disease, each robot, each person, each language, each task.

Q: *What if your data has a long tail?*
A: This segng breaks standard machine learning paradigms.

Q: *What if you need to quickly learn something new?*
A: by leveraging prior experience!

Q: *What if you want a more general-purpose AI system?*
A: Learning each task from scratch won’t cut it

**This is where elements of mul--task learning can come into play.**

### 2. What is a task?

Before Learning: $\text{dataset } \mathcal{D} , \text{loss func } \mathcal{L}  \to \text{model } f_\theta$

Different tasks can vary based on:
- different objects
- different people
- different objectives
- different lightning conditions
- different words
- different languages

### 3. Critical Assumption

Different tasks need to share some structure.
不同的任务需要有相同的结构。不然最好还是单任务处理。

There are many tasks with shared structure!
索性还是有很多任务有相同的结构的（即使看上去没有关系）。

### 4. Why now?

Q: *Why should we study deep multi-task & meta-learning now?*
A: 
 - These algorithms are playing a fundamental, and increasing role in machine learning research.
 - Its success will be cri,cal for the democratization of deep learning.
(废话)

### [General Multi-Task Learning](.md) vs Multi-scenario modeling

来自Domain adaptation的[Multi-scenario modeling](../Domain%20adaptation/Multi-scenario%20modeling/Multi-scenario%20modeling.md)与广义多任务学习虽然看上去类似，但有一些重要的区别。在多任务学习、元学习和迁移学习中，模型通常是在任务级别进行学习和适应的，而在多场景建模中，模型是在场景级别进行学习和适应的。这意味着多场景建模需要处理的问题可能更加复杂，因为场景之间的差异可能比任务之间的差异更大。

因此，虽然多场景建模与多任务学习、元学习和迁移学习有一些共性，但由于它们处理的问题和挑战有所不同，所以它们通常被视为不同的研究领域。









## 4. Advanced meta-learning topics (meta-overfitting, unsupervised meta-learning, Bayesian models) 

5. Multi-task RL, goal-conditioned RL 

6. Meta-reinforcement learning 

7. Hierarchical RL 

8. Lifelong learning 

9. Open problems