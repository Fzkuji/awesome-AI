## Tutorials

- [Learning Prompt](https://learningprompt.wiki/)
	- 免费的Prompt Engineering教程
- [Learn Prompting](https://learnprompting.org/)
	- 另一个免费的Prompting教程

## Prompt

### Classification

- Hard/discrete prompt
	- 使用单词的prompt，人仍然可以理解
- Soft continuous prompt
	- 抽象为参数的prompt，人无法理解，解释性差
- Prefix prompt
	- 为一个任务专门设计的、抽象为参数的prompt，不仅人无法理解，不同LLMs模型也无法互相理解

## History

Prompt-engineering emerged with the release of OpenAI’s GPT (Generative Pretrained Transformer), a behemoth 10 times larger than any previous language model. In a [2020 paper](https://papers.nips.cc/paper/2020/hash/1457c0d6bfcb4967418bfb8ac142f64a-Abstract.html), researchers at OpenAI showed that the scale of GPT’s successor, GPT-3, at 175 billion parameters, allowed it to perform specialized tasks with only a smattering of words introduced at inference time. With no retraining, GPT-3 performed nearly as well as a model fine-tuned on labeled data.

Initially, prompts were designed by hand, through what's known as prompt-engineering. 

> Let’s say you want to adapt a language model for translation tasks. You give the model a description of the target task or a few examples. “Translate English to French,” for example, with the prompt: “cheese.” The model then outputs its prediction: “fromage.” 

This manual prompt primes the model to retrieve from its memory banks other words in French. If the task is difficult enough, dozens of prompts might be needed.

Hand-crafted prompts were quickly replaced by superior AI-designed prompts consisting of strings of numbers. In [a paper](https://aclanthology.org/2021.emnlp-main.243.pdf) the following year, Google researchers introduced so-called “soft” prompts designed by an AI that outperformed human-engineered “hard” prompts.

Unlike hard prompts, AI-designed soft prompts are unrecognizable to the human eye. Each prompt consists of an embedding, or string of numbers, that distills knowledge from the larger model. High level or task specific, the prompt acts as a substitute for additional training data. Researchers recently [estimated](https://aclanthology.org/2021.naacl-main.208.pdf) that a good language classifier prompt is worth hundreds to thousands of extra data points.

Around the same time, Stanford researchers introduced [Prefix-tuning](+Papers/Prefix-tuning.md), another automated prompt-design method that allows the model to learn one task after another. Prefix-tuning combines soft prompts with prompts injected into layers of the deep learning model for added flexibility. Though prompt-tuning is more efficient, both techniques let you freeze the model and skip expensive retraining.

One drawback of prompt-tuning is its lack of interpretability. The AI discovers prompts optimized for a given task but can’t explain why it chose those embeddings. Like deep learning models themselves, soft prompts are opaque.


## Prompt engineering

以下内容存疑

### Prompt tuning

![Parameter-efficient prompt tuning](../../../../../Resources/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Natural%20language%20processing/Large%20language%20model/Parameter-efficient%20prompt%20tuning.png)

Parameter-efficient prompt tuning from https://people.cs.umass.edu/~miyyer/cs685/slides/prompt_learning.pdf

视频讲解：[](Advanced%20NLP)%20#12:%20Prompt-based%20learning%5D(https://www.youtube.com/watch?v=8HwHGGb1zpQ%7CUMass%20CS685%20S22%20(Advanced%20NLP)%20#12:%20Prompt-based%20learning%5D(https://www.youtube.com/watch?v=8HwHGGb1zpQ%7CUMass%20CS685%20S22%20(Advanced%20NLP)%20#12:%20Prompt-based%20learning)

Prompt tuning is a technique used to fine-tune large pre-trained language models, such as those based on the GPT architecture, to specific tasks or domains. Instead of fine-tuning the entire model, prompt tuning involves learning a small set of "prompts" or input transformations that guide the model's behavior during inference. This approach aims to achieve good performance on the target task while minimizing the amount of training data and computational resources required.

In traditional fine-tuning, the entire model, including all its parameters, is updated using a task-specific dataset to adapt the model to a particular task. This process can be computationally expensive and may require a large amount of training data.

Prompt tuning, on the other hand, leverages the fact that pre-trained language models already possess a wealth of general knowledge and linguistic understanding. By learning a set of task-specific input transformations or prompts, the model can be guided to generate correct outputs for the target task using its existing knowledge.

Here's a detailed explanation of the prompt tuning process:

1.  Start with a pre-trained language model: Prompt tuning begins with a large pre-trained language model that has been trained on a diverse corpus of text and has already learned general language understanding and knowledge.
2.  Define the target task: Determine the specific task or domain you want the model to adapt to. This could be anything from question-answering, summarization, translation, sentiment analysis, or other NLP tasks.
3.  Identify candidate prompts: Create a set of candidate prompts for the target task. These prompts can be manually designed, based on expert knowledge, or generated using more systematic approaches, like searching over possible input transformations. Prompts serve as templates that guide the model's behavior during inference, by transforming the input in a way that directs the model to generate the desired output.
4.  Train a prompt selector or learn a parameterized prompt: To identify the optimal prompt(s) from the candidate set, you can train a small neural network or parameterized function to select or transform the input prompt based on the task input. This is done using a task-specific dataset, where the goal is to minimize the loss function associated with the target task.
5.  Fine-tune the prompt: Optionally, you can fine-tune the prompt during the training process by updating the prompt tokens' embeddings in the pre-trained model. This step can help the model generate better outputs for the target task while still maintaining its general language understanding.
6.  Perform inference using the learned prompt: Once the optimal prompt(s) or prompt transformation function has been learned, you can use it during inference to guide the pre-trained language model's behavior. This involves providing the transformed input, which includes the selected or transformed prompt, to the model and generating the output based on the model's response.

Prompt tuning offers several advantages over traditional fine-tuning, including reduced computational requirements, the ability to adapt to tasks with limited training data, and the potential for better transfer learning to related tasks. However, it may not always achieve the same level of performance as full-model fine-tuning, especially for tasks that require substantial adaptation from the pre-trained model's initial knowledge.

Q: So the prompt selector get the type of the task and output some prompt?

Yes, that's one way to implement a prompt selector. The prompt selector can take the task type or some information about the task as input and output a suitable prompt to be used with the pre-trained language model. By learning the optimal prompt for a given task, the prompt selector helps guide the language model to generate the desired output.

In some cases, the prompt selector can be more dynamic, taking both the task type and the specific input instance into consideration. This allows the prompt selector to adapt the prompt based on the content of the input, potentially leading to better performance across a range of inputs.

It's important to note that the prompt selector itself is usually a small neural network or parameterized function that is trained using a task-specific dataset. Once trained, the prompt selector can be used during inference to generate the optimal prompt for a given task or input instance, which is then combined with the input to guide the pre-trained language model's behavior.

### Prefix-tuning

Prompt Tuning and Prefix-Tuning are two different approaches to adapt pre-trained language models (such as GPT) for specific tasks. Both methods involve using prompts to guide the model's behavior, but they differ in the way they represent and optimize these prompts.

1.  Prompt Tuning: In Prompt Tuning, discrete textual prompts are used to guide the model's behavior. These prompts are manually crafted or selected from a set of pre-defined templates and are combined with the input during the inference process. The main idea behind Prompt Tuning is to find an optimal discrete prompt for a specific task by experimenting with different prompt variations or by using techniques like reinforcement learning. The main model parameters remain fixed during the tuning process, and the focus is on finding the most effective textual prompt to use with the pre-trained model.
2.  Prefix-Tuning: In Prefix-Tuning, continuous prompts are used instead of discrete textual prompts. Continuous prompts are learnable embeddings that are prepended to the input sequence, allowing them to interact with the main model parameters through the model's self-attention mechanism. Unlike Prompt Tuning, where the prompts are fixed and manually crafted, Prefix-Tuning involves fine-tuning the continuous prompts by updating their embeddings during the training process. The main model parameters remain fixed, and only the continuous prompt embeddings are updated.

The main differences between Prompt Tuning and Prefix-Tuning are:

-   Discrete vs. Continuous Prompts: Prompt Tuning uses discrete textual prompts, while Prefix-Tuning uses continuous, learnable prompt embeddings.
-   Optimization: In Prompt Tuning, the focus is on finding the best discrete prompt for a given task, whereas, in Prefix-Tuning, the continuous prompt embeddings are optimized during the training process.
-   Flexibility: Prefix-Tuning offers more flexibility compared to Prompt Tuning, as the continuous prompts can be learned and adapted to specific tasks more effectively than discrete textual prompts.

Overall, both Prompt Tuning and Prefix-Tuning are methods for adapting pre-trained language models to specific tasks. While Prompt Tuning relies on finding the best discrete textual prompt, Prefix-Tuning leverages continuous, learnable prompt embeddings to fine-tune the model's behavior more effectively.


## Papers

[Prefix-tuning](+Papers/Prefix-tuning.md)
[Prefix-tuning v2](+Papers/Prefix-tuning%20v2.md)
[Transferability of prompt tuning](+Papers/Transferability%20of%20prompt%20tuning.md)


