## [Diffusion漫谈](https://kexue.fm/archives/9164)

可以看这一整个系列，介绍了很多diffusion的概念

### [DDPM = 贝叶斯 + 去噪](https://kexue.fm/archives/9164)

目前所采用的扩散模型大都是来自于2020年的工作[DDPM: Denoising Diffusion Probabilistic Models](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2006.11239)，DDPM对之前的扩散模型（具体见[Deep Unsupervised Learning using Nonequilibrium Thermodynamics](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1503.03585)）进行了简化，并通过**变分推断**（variational inference）来进行建模，这主要是因为扩散模型也是一个**隐变量模型**（latent variable model），相比VAE这样的隐变量模型，扩散模型的隐变量是和原始数据是同维度的，而且推理过程（即扩散过程）往往是固定的。

DDPM的总流程如下：
$$\boldsymbol{x} = \boldsymbol{x}_0 \rightleftharpoons \boldsymbol{x}_1 \rightleftharpoons \boldsymbol{x}_2 \rightleftharpoons \cdots \rightleftharpoons \boldsymbol{x}_{T-1} \rightleftharpoons \boldsymbol{x}_T = \boldsymbol{z}$$
单步前向加噪公式如下：
$$
\begin{aligned}
\mathbf{x}_t 
&= \sqrt{\alpha_t}\mathbf{x}_{t-1} + \sqrt{1 - \alpha_t}\boldsymbol{\epsilon}_{t-1} & \text{ ;where } \boldsymbol{\epsilon}_{t-1}, \boldsymbol{\epsilon}_{t-2}, \dots \sim \mathcal{N}(\mathbf{0}, \mathbf{I}) \\
&= \sqrt{\alpha_t \alpha_{t-1}} \mathbf{x}_{t-2} + \sqrt{1 - \alpha_t \alpha_{t-1}} \bar{\boldsymbol{\epsilon}}_{t-2} & \text{ ;where } \bar{\boldsymbol{\epsilon}}_{t-2} \text{ merges two Gaussians (*).} \\
&= \dots \\
&= \sqrt{\bar{\alpha}_t}\mathbf{x}_0 + \sqrt{1 - \bar{\alpha}_t}\boldsymbol{\epsilon} \\
q(\mathbf{x}_t \vert \mathbf{x}_0) &= \mathcal{N}(\mathbf{x}_t; \sqrt{\bar{\alpha}_t} \mathbf{x}_0, (1 - \bar{\alpha}_t)\mathbf{I})
\end{aligned}
$$
其中$\alpha_t = 1 - \beta_t$，$\bar{\alpha}_t = \prod_{i=1}^t \alpha_i$

求解目标为单步反向降噪：
$$p(\boldsymbol{x}_{t-1}|\boldsymbol{x}_t) = \frac{p(\boldsymbol{x}_t|\boldsymbol{x}_{t-1})p(\boldsymbol{x}_{t-1})}{p(\boldsymbol{x}_t)}$$
但是其中只有$p(\boldsymbol{x}_t|\boldsymbol{x}_{t-1})$是已知的，因此DDPM退而求其次，在给定$\boldsymbol{x}_0$的条件下使用贝叶斯定理：
$$
\begin{aligned}
p(\boldsymbol{x}_{t-1}|\boldsymbol{x}_t, \boldsymbol{x}_0)
&= \frac{p(\boldsymbol{x}_t|\boldsymbol{x}_{t-1}, \boldsymbol{x}_0)p(\boldsymbol{x}_{t-1}|\boldsymbol{x}_0)}{p(\boldsymbol{x}_t|\boldsymbol{x}_0)} \\
&= \frac{p(\boldsymbol{x}_t|\boldsymbol{x}_{t-1})p(\boldsymbol{x}_{t-1}|\boldsymbol{x}_0)}{p(\boldsymbol{x}_t|\boldsymbol{x}_0)} \\
&\propto \exp \Big(-\frac{1}{2} \big(\frac{(\mathbf{x}_t - \sqrt{\alpha_t} \mathbf{x}_{t-1})^2}{\beta_t} + \frac{(\mathbf{x}_{t-1} - \sqrt{\bar{\alpha}_{t-1}} \mathbf{x}_0)^2}{1-\bar{\alpha}_{t-1}} - \frac{(\mathbf{x}_t - \sqrt{\bar{\alpha}_t} \mathbf{x}_0)^2}{1-\bar{\alpha}_t} \big) \Big) \\
&= \exp \Big(-\frac{1}{2} \big(\frac{\mathbf{x}_t^2 - 2\sqrt{\alpha_t} \mathbf{x}_t \color{blue}{\mathbf{x}_{t-1}} \color{black}{+ \alpha_t} \color{red}{\mathbf{x}_{t-1}^2} }{\beta_t} + \frac{ \color{red}{\mathbf{x}_{t-1}^2} \color{black}{- 2 \sqrt{\bar{\alpha}_{t-1}} \mathbf{x}_0} \color{blue}{\mathbf{x}_{t-1}} \color{black}{+ \bar{\alpha}_{t-1} \mathbf{x}_0^2}  }{1-\bar{\alpha}_{t-1}} - \frac{(\mathbf{x}_t - \sqrt{\bar{\alpha}_t} \mathbf{x}_0)^2}{1-\bar{\alpha}_t} \big) \Big) \\
&= \exp\Big( -\frac{1}{2} \big( \color{red}{(\frac{\alpha_t}{\beta_t} + \frac{1}{1 - \bar{\alpha}_{t-1}})} \mathbf{x}_{t-1}^2 - \color{blue}{(\frac{2\sqrt{\alpha_t}}{\beta_t} \mathbf{x}_t + \frac{2\sqrt{\bar{\alpha}_{t-1}}}{1 - \bar{\alpha}_{t-1}} \mathbf{x}_0)} \mathbf{x}_{t-1} \color{black}{ + C(\mathbf{x}_t, \mathbf{x}_0) \big) \Big)}
\end{aligned}
$$
其中：
$$
\begin{align}
& p(\boldsymbol{x}_{t-1}|\boldsymbol{x}_0) = \sqrt{\bar{\alpha}_{t-1}} \mathbf{x}_0 + \sqrt{1-\bar{\alpha}_{t-1}} z \sim \mathcal{N} \left ( \sqrt{\bar{\alpha}_{t-1}}, 1-\bar{\alpha}_{t-1} \right ) \\
& p(\boldsymbol{x}_t|\boldsymbol{x}_0) = \sqrt{\bar{\alpha}_t} \mathbf{x}_0 + \sqrt{1-\bar{\alpha}_t} z \sim \mathcal{N} \left ( \sqrt{\bar{\alpha}_t}, 1-\bar{\alpha}_t \right ) \\
& p(\boldsymbol{x}_{t-1}|\boldsymbol{x}_0) = \sqrt{\alpha_{t-1}} \mathbf{x}_0 + \sqrt{1-\alpha_{t-1}} z \sim \mathcal{N} \left ( \sqrt{\alpha_{t-1} }, 1-{\alpha}_{t-1} \right )
\end{align}
$$
对于，我们知道它是服从正态分布的，因此，根据正态分布的公式：
$$
\mathrm{exp} \big ( -\frac{(x-\mu )^2}{2\sigma^2} \big ) = \mathrm{exp} \Big ( - \frac{1}{2} \big ( \frac{1}{\sigma ^2} x^2 - \frac{2\mu}{\sigma^2} x + \frac{\mu^2}{\sigma^2} \big )  \Big  )
$$
其中$\sigma^2$是方差，$\mu$是均值。因此相对应的，可以得到$p(\boldsymbol{x}_{t-1}|\boldsymbol{x}_t, \boldsymbol{x}_0)$的方差$\tilde{\beta}_t$和均值$\tilde{\boldsymbol{\mu}}_t (\mathbf{x}_t, \mathbf{x}_0)$：
$$
\begin{aligned}
\tilde{\beta}_t 
&= 1/(\frac{\alpha_t}{\beta_t} + \frac{1}{1 - \bar{\alpha}_{t-1}}) 
= 1/(\frac{\alpha_t - \bar{\alpha}_t + \beta_t}{\beta_t(1 - \bar{\alpha}_{t-1})})
= \color{green}{\frac{1 - \bar{\alpha}_{t-1}}{1 - \bar{\alpha}_t} \cdot \beta_t} \\
\tilde{\boldsymbol{\mu}}_t (\mathbf{x}_t, \mathbf{x}_0)
&= (\frac{\sqrt{\alpha_t}}{\beta_t} \mathbf{x}_t + \frac{\sqrt{\bar{\alpha}_{t-1} }}{1 - \bar{\alpha}_{t-1}} \mathbf{x}_0)/(\frac{\alpha_t}{\beta_t} + \frac{1}{1 - \bar{\alpha}_{t-1}}) \\
&= (\frac{\sqrt{\alpha_t}}{\beta_t} \mathbf{x}_t + \frac{\sqrt{\bar{\alpha}_{t-1} }}{1 - \bar{\alpha}_{t-1}} \mathbf{x}_0) \color{green}{\frac{1 - \bar{\alpha}_{t-1}}{1 - \bar{\alpha}_t} \cdot \beta_t} \\
&= \frac{\sqrt{\alpha_t}(1 - \bar{\alpha}_{t-1})}{1 - \bar{\alpha}_t} \mathbf{x}_t + \frac{\sqrt{\bar{\alpha}_{t-1}}\beta_t}{1 - \bar{\alpha}_t} \mathbf{x}_0\\
\end{aligned}
$$
其中未知数是$\boldsymbol{x}_0$，又因为：
$$p(\boldsymbol{x}_t|\boldsymbol{x}_0)=\mathcal{N}(\boldsymbol{x}_t;\bar{\alpha}_t \boldsymbol{x}_0,\bar{\beta}_t^2 \boldsymbol{I})$$
$$\boldsymbol{x}_t = \bar{\alpha}_t \boldsymbol{x}_0 + \bar{\beta}_t \boldsymbol{\varepsilon},\boldsymbol{\varepsilon}\sim\mathcal{N}(\boldsymbol{0}, \boldsymbol{I})$$
所以可得：
$$
\boldsymbol{x}_0 = \frac{1}{\bar{\alpha}_t}\left(\boldsymbol{x}_t  - \bar{\beta}_t \boldsymbol{\varepsilon}\right)
$$
所以，最终要求的目标是$\boldsymbol{\varepsilon}$，也就是forward过程中加的噪声。最终模型的损失函数如下：
$$
\begin{aligned}
L_t^\text{simple}
&= \mathbb{E}_{t \sim [1, T], \mathbf{x}_0, \boldsymbol{\epsilon}_t} \Big[\|\boldsymbol{\epsilon}_t - \boldsymbol{\epsilon}_\theta(\mathbf{x}_t, t)\|^2 \Big] \\
&= \mathbb{E}_{t \sim [1, T], \mathbf{x}_0, \boldsymbol{\epsilon}_t} \Big[\|\boldsymbol{\epsilon}_t - \boldsymbol{\epsilon}_\theta(\sqrt{\bar{\alpha}_t}\mathbf{x}_0 + \sqrt{1 - \bar{\alpha}_t}\boldsymbol{\epsilon}_t, t)\|^2 \Big]
\end{aligned}
$$
其中，$\boldsymbol{\epsilon}_t$是噪声真实值，$\boldsymbol{\epsilon}_\theta(\mathbf{x}_t, t)$是基于输入$\mathbf{x}_t, t$的模型预测值。

### [从DDPM到DDIM](https://kexue.fm/archives/9181)

DDPM是最基本的扩散模型。DDPM在生成（或者叫采样）的过程中仍然会加入随机值，因此即使有相同的输入也会有不同的输出。但是DDIM进行了简化，将随即部分省略，结果就是相同的输入模型会给出相同的输出。
这是扩散模型的采样结果：
$$\begin{aligned} 
p(\boldsymbol{x}_{t-1}|\boldsymbol{x}_t) \approx&\, p(\boldsymbol{x}_{t-1}|\boldsymbol{x}_t, \boldsymbol{x}_0=\bar{\boldsymbol{\mu}}(\boldsymbol{x}_t)) \\ 
=&\, \mathcal{N}\left(\boldsymbol{x}_{t-1}; \frac{1}{\alpha_t}\left(\boldsymbol{x}_t - \left(\bar{\beta}_t - \alpha_t\sqrt{\bar{\beta}_{t-1}^2 - \sigma_t^2}\right) \boldsymbol{\epsilon}_{\boldsymbol{\theta}}(\boldsymbol{x}_t, t)\right), \sigma_t^2 \boldsymbol{I}\right) 
\end{aligned}$$
 - 如果使$\sigma_t^2=0$，那么模型就变成了DDIM
 - 如果使$\sigma_t = \frac{\bar{\beta}_{t-1}\beta_t}{\bar{\beta}_t}$，那么模型就是DDPM

### [DPM-solver](https://www.bilibili.com/video/BV1B24y1Q7wZ/)

**这个还没看，有空看**

## 研究提案


Diffusion model是一个最近流行的机器学习模型，其目前广泛应用于图像生成、去噪、超分辨率等领域并取得了优异的效果。

### 加速算法

Let: 
$$
\begin{aligned}
&\alpha_t = 1 - \beta_t \\
&\alpha_{t}^{t+n}  = \prod_{i=t}^{t+n} \alpha_i \\
\end{aligned}
$$
既然我们有
$$
\begin{aligned}
p(\boldsymbol{x}_{t-1}|\boldsymbol{x}_t, \boldsymbol{x}_0)
&= \frac{p(\boldsymbol{x}_t|\boldsymbol{x}_{t-1}, \boldsymbol{x}_0)p(\boldsymbol{x}_{t-1}|\boldsymbol{x}_0)}{p(\boldsymbol{x}_t|\boldsymbol{x}_0)} \\
&= \frac{p(\boldsymbol{x}_t|\boldsymbol{x}_{t-1})p(\boldsymbol{x}_{t-1}|\boldsymbol{x}_0)}{p(\boldsymbol{x}_t|\boldsymbol{x}_0)} \\
&\propto \exp \Big(-\frac{1}{2} \big(\frac{(\mathbf{x}_t - \sqrt{\alpha_t} \mathbf{x}_{t-1})^2}{\beta_t} + \frac{(\mathbf{x}_{t-1} - \sqrt{\bar{\alpha}_{t-1}} \mathbf{x}_0)^2}{1-\bar{\alpha}_{t-1}} - \frac{(\mathbf{x}_t - \sqrt{\bar{\alpha}_t} \mathbf{x}_0)^2}{1-\bar{\alpha}_t} \big) \Big) \\
&= \exp \Big(-\frac{1}{2} \big(\frac{\mathbf{x}_t^2 - 2\sqrt{\alpha_t} \mathbf{x}_t \color{blue}{\mathbf{x}_{t-1}} \color{black}{+ \alpha_t} \color{red}{\mathbf{x}_{t-1}^2} }{\beta_t} + \frac{ \color{red}{\mathbf{x}_{t-1}^2} \color{black}{- 2 \sqrt{\bar{\alpha}_{t-1}} \mathbf{x}_0} \color{blue}{\mathbf{x}_{t-1}} \color{black}{+ \bar{\alpha}_{t-1} \mathbf{x}_0^2}  }{1-\bar{\alpha}_{t-1}} - \frac{(\mathbf{x}_t - \sqrt{\bar{\alpha}_t} \mathbf{x}_0)^2}{1-\bar{\alpha}_t} \big) \Big) \\
&= \exp\Big( -\frac{1}{2} \big( \color{red}{(\frac{\alpha_t}{\beta_t} + \frac{1}{1 - \bar{\alpha}_{t-1}})} \mathbf{x}_{t-1}^2 - \color{blue}{(\frac{2\sqrt{\alpha_t}}{\beta_t} \mathbf{x}_t + \frac{2\sqrt{\bar{\alpha}_{t-1}}}{1 - \bar{\alpha}_{t-1}} \mathbf{x}_0)} \mathbf{x}_{t-1} \color{black}{ + C(\mathbf{x}_t, \mathbf{x}_0) \big) \Big)}
\end{aligned}
$$
那么同理可得：
$$
\begin{aligned}
p(\boldsymbol{x}_{t-n}|\boldsymbol{x}_t, \boldsymbol{x}_0)

&= \frac{p(\boldsymbol{x}_t|\boldsymbol{x}_{t-n}, \boldsymbol{x}_0)p(\boldsymbol{x}_{t-n}|\boldsymbol{x}_0)}{p(\boldsymbol{x}_t|\boldsymbol{x}_0)} \\

&= \frac{p(\boldsymbol{x}_t|\boldsymbol{x}_{t-n})p(\boldsymbol{x}_{t-n}|\boldsymbol{x}_0)}{p(\boldsymbol{x}_t|\boldsymbol{x}_0)} \\

&\propto \exp \Big(-\frac{1}{2} \big(\frac{(\mathbf{x}_t - \sqrt{\alpha_{t-n+1}^{t}} \mathbf{x}_{t-n})^2}{1-\alpha_{t-n+1}^{t}} + \frac{(\mathbf{x}_{t-n} - \sqrt{\alpha_{1}^{t-n}} \mathbf{x}_0)^2}{1-\alpha_{1}^{t-n}} - \frac{(\mathbf{x}_t - \sqrt{\alpha_1^t} \mathbf{x}_0)^2}{1-\alpha_{1}^{t}} \big) \Big) \\

&= \exp \Big(-\frac{1}{2} \big(\frac{\mathbf{x}_t^2 - 2\sqrt{\alpha_{t-n+1}^{t}} \mathbf{x}_t \color{blue}{\mathbf{x}_{t-n}} \color{black}{+ \alpha_{t-n+1}^{t}} \color{red}{\mathbf{x}_{t-n}^2} }{1-\alpha_{t-n+1}^{t}} + \frac{ \color{red}{\mathbf{x}_{t-n}^2} \color{black}{- 2 \sqrt{\alpha_{1}^{t-n}} \mathbf{x}_0} \color{blue}{\mathbf{x}_{t-n}} \color{black}{+ \alpha_{1}^{t-n} \mathbf{x}_0^2}  }{1-\alpha_{1}^{t-n}} - \frac{(\mathbf{x}_t - \sqrt{\alpha_{1}^{t}} \mathbf{x}_0)^2}{1-\alpha_{1}^{t}} \big) \Big) \\

&= \exp\Big( -\frac{1}{2} \big( \color{red}{(\frac{\alpha_{t-n+1}^{t}}{1-\alpha_{t-n+1}^{t}} + \frac{1}{1 - \alpha_{1}^{t-n}})} \mathbf{x}_{t-n}^2 - \color{blue}{(\frac{2\sqrt{\alpha_{t-n+1}^{t}}}{1-\alpha_{t-n+1}^{t}} \mathbf{x}_t + \frac{2\sqrt{\alpha_{1}^{t-n}}}{1 - \alpha_{1}^{t-n}} \mathbf{x}_0)} \mathbf{x}_{t-n} \color{black}{ + C(\mathbf{x}_t, \mathbf{x}_0) \big) \Big)} \\

&= \exp\Big( -\frac{1}{2} \big( \color{red}{\frac{1-\alpha_{1}^{t}}{1-\alpha_{1}^{t-n}-\alpha_{t-n+1}^{t}+\alpha_{1}^{t}}} \mathbf{x}_{t-n}^2 - \color{blue}{(\frac{2\sqrt{\alpha_{t-n+1}^{t}}}{1-\alpha_{t-n+1}^{t}} \mathbf{x}_t + \frac{2\sqrt{\alpha_{1}^{t-n}}}{1 - \alpha_{1}^{t-n}} \mathbf{x}_0)} \mathbf{x}_{t-n} \color{black}{ + C(\mathbf{x}_t, \mathbf{x}_0) \big) \Big)} \\

&= \mathcal{N}\left(\boldsymbol{x}_{t-n}; \mu = \color{blue}{(\frac{\sqrt{\alpha_{t-n+1}^{t}}}{1-\alpha_{t-n+1}^{t}} \mathbf{x}_t + \frac{\sqrt{\alpha_{1}^{t-n}}}{1 - \alpha_{1}^{t-n}} \mathbf{x}_0)} {\frac{1-\alpha_{1}^{t-n}-\alpha_{t-n+1}^{t}+\alpha_{1}^{t}}{1-\alpha_{1}^{t}}} \color{black}, \sigma_t^2 = \color{red}{\frac{1-\alpha_{1}^{t-n}-\alpha_{t-n+1}^{t}+\alpha_{1}^{t}}{1-\alpha_{1}^{t}}} \color{black}\right) 

\end{aligned}
$$

$$
\mathrm{exp} \big ( -\frac{(x-\mu )^2}{2\sigma^2} \big ) = \mathrm{exp} \Big ( - \frac{1}{2} \big ( \frac{1}{\sigma ^2} x^2 - \frac{2\mu}{\sigma^2} x + \frac{\mu^2}{\sigma^2} \big )  \Big  )
$$

#### 目前的缺陷解决

There are several possible ways to address the limitations of current diffusion models:

1.  Developing new diffusion models or variations of existing models that are better suited for tasks such as image classification or segmentation. This could involve incorporating additional information about the image or using more complex diffusion equations that are able to capture more complex relationships in the data.
2.  Developing diffusion models that are able to handle video or other temporal data, by extending the model to operate on a sequence of images or by using temporal information in the diffusion process.
3.  Developing diffusion models that are able to handle 3D data, such as 3D images or point clouds. This could involve adapting the model to operate on 3D data or by using techniques such as volumetric convolutions.
4.  Combining diffusion models with other types of models or techniques, such as convolutional neural networks (CNNs) or graph convolutional networks (GCNs), to create hybrid models that can make use of the strengths of both approaches.
5.  Developing more effective methods for incorporating additional information into diffusion models, such as using embeddings or directly inputting additional images or tensors.
6.  Improving the efficiency and scalability of diffusion models, to make them more practical for use in real-world applications.