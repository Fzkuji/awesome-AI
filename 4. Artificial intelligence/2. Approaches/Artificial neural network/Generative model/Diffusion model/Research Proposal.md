## Image Conditional Diffusion Model for Supervised Learning

### Introduction

Diffusion models are a class of latent variable generative models inspired by non-equilibrium thermodynamics. They have been widely used for image-processing tasks such as denoising and inpainting. Most currently used diffusion models were from the working [denoising diffusion probabilistic models (DDPM)](DDPM)) in 2020 and generally required 1000-step iterations, which consume much time for training and prediction. Therefore, many subsequent diffusion models focus on accelerating sampling techniques to reduce the time consumed by generation.

Shortly, a new member of the diffusion model family, the [denoising diffusion implicit model (DDIM)](DDIM)), achieved state-of-the-art performance on snapshot denoising tasks and reduced the number of iterations to 100 steps by generalizing the diffusion process. However, one limitation of DDIM and other diffusion models is their inability to incorporate additional information about the image, such as the presence of certain object classes or features. 

There have been works trying to introduce the diffusion model into supervised learning, mainly in image segmentation, such as [MedSegDiff](https://arxiv.org/abs/2211.00611) and [SegDiff](https://arxiv.org/abs/2112.00390). However, it can only solve simple tasks, and the diffusion models have a slight advantage over complex scenes over existing segmentation algorithms, especially in terms of real-time performance. In this proposal, we propose to extend DDIM by adding a conditional image to the model, allowing it to use this additional information for improved performance on image segmentation tasks in complex scenes.

### Background

#### Diffusion Models

In the original paper of DDPM, Bayesian theorem is used to explain the diffusion process. 
$$\boldsymbol{x} = \boldsymbol{x}_0 \rightleftharpoons \boldsymbol{x}_1 \rightleftharpoons \boldsymbol{x}_2 \rightleftharpoons \cdots \rightleftharpoons \boldsymbol{x}_{T-1} \rightleftharpoons \boldsymbol{x}_T = \boldsymbol{z}$$
where $\boldsymbol{x}$ is the original image and $\boldsymbol{z}$ is pure noise. $\boldsymbol{x}_{t\in  (0,T)}$ is the result of one step of adding noise and denoising, whose formulas are as follows respectively [(refer to Su's blog post)](refer%20to%20Su's%20blog%20post)): 
$$
\begin{aligned}
q(\mathbf{x}_t \vert \mathbf{x}_0)
&= \sqrt{\alpha_t}\mathbf{x}_{t-1} + \sqrt{1 - \alpha_t}\boldsymbol{\epsilon}_{t-1} & \text{ ;where } \boldsymbol{\epsilon}_{t-1}, \boldsymbol{\epsilon}_{t-2}, \dots \sim \mathcal{N}(\mathbf{0}, \mathbf{I}) \\
&= \sqrt{\alpha_t \alpha_{t-1}} \mathbf{x}_{t-2} + \sqrt{1 - \alpha_t \alpha_{t-1}} \bar{\boldsymbol{\epsilon}}_{t-2} & \text{ ;where } \bar{\boldsymbol{\epsilon}}_{t-2} \text{ merges two Gaussians (*).} \\
&= \dots \\
&= \sqrt{\bar{\alpha}_t}\mathbf{x}_0 + \sqrt{1 - \bar{\alpha}_t}\boldsymbol{\epsilon} \\
&= \mathcal{N}(\mathbf{x}_t; \sqrt{\bar{\alpha}_t} \mathbf{x}_0, (1 - \bar{\alpha}_t)\mathbf{I})
\end{aligned}
$$
$$
\begin{aligned}
p(\boldsymbol{x}_{t-1}|\boldsymbol{x}_t)
&= p(\boldsymbol{x}_{t-1}|\boldsymbol{x}_t, \boldsymbol{x}_0) \\
&= \frac{p(\boldsymbol{x}_t|\boldsymbol{x}_{t-1}, \boldsymbol{x}_0)p(\boldsymbol{x}_{t-1}|\boldsymbol{x}_0)}{p(\boldsymbol{x}_t|\boldsymbol{x}_0)} \\
&= \frac{p(\boldsymbol{x}_t|\boldsymbol{x}_{t-1})p(\boldsymbol{x}_{t-1}|\boldsymbol{x}_0)}{p(\boldsymbol{x}_t|\boldsymbol{x}_0)} \\
&\propto \exp \Big(-\frac{1}{2} \big(\frac{(\mathbf{x}_t - \sqrt{\alpha_t} \mathbf{x}_{t-1})^2}{\beta_t} + \frac{(\mathbf{x}_{t-1} - \sqrt{\bar{\alpha}_{t-1}} \mathbf{x}_0)^2}{1-\bar{\alpha}_{t-1}} - \frac{(\mathbf{x}_t - \sqrt{\bar{\alpha}_t} \mathbf{x}_0)^2}{1-\bar{\alpha}_t} \big) \Big) \\
&= \exp \Big(-\frac{1}{2} \big(\frac{\mathbf{x}_t^2 - 2\sqrt{\alpha_t} \mathbf{x}_t \color{blue}{\mathbf{x}_{t-1}} \color{black}{+ \alpha_t} \color{red}{\mathbf{x}_{t-1}^2} }{\beta_t} + \frac{ \color{red}{\mathbf{x}_{t-1}^2} \color{black}{- 2 \sqrt{\bar{\alpha}_{t-1}} \mathbf{x}_0} \color{blue}{\mathbf{x}_{t-1}} \color{black}{+ \bar{\alpha}_{t-1} \mathbf{x}_0^2}  }{1-\bar{\alpha}_{t-1}} - \frac{(\mathbf{x}_t - \sqrt{\bar{\alpha}_t} \mathbf{x}_0)^2}{1-\bar{\alpha}_t} \big) \Big) \\
&= \exp\Big( -\frac{1}{2} \big( \color{red}{(\frac{\alpha_t}{\beta_t} + \frac{1}{1 - \bar{\alpha}_{t-1}})} \mathbf{x}_{t-1}^2 - \color{blue}{(\frac{2\sqrt{\alpha_t}}{\beta_t} \mathbf{x}_t + \frac{2\sqrt{\bar{\alpha}_{t-1}}}{1 - \bar{\alpha}_{t-1}} \mathbf{x}_0)} \mathbf{x}_{t-1} \color{black}{ + C(\mathbf{x}_t, \mathbf{x}_0) \big) \Big)}
\end{aligned}
$$
According to [Yang](https://arxiv.org/abs/2011.13456), DDPM can be generalized into a more theoretical framework of generative diffusion model - stochastic differential equation (SDE). The forward noise adding process described using SDE is expressed as follows:
$$
d\boldsymbol{x} = \boldsymbol{f}_t(\boldsymbol{x}) dt + g_t d\boldsymbol{w}
$$
The reverse denoising process formula is:
$$d\boldsymbol{x} = \left[\boldsymbol{f}_t(\boldsymbol{x}) - g_t^2\nabla_{\boldsymbol{x}}\log p_t(\boldsymbol{x}) \right] dt + g_t d\boldsymbol{w}$$
where $\boldsymbol{w}$  is the standard Wiener process (a.k.a., Brownian motion), $f(\cdot,t ):\mathbb{R}^d\to \mathbb{R}^d$ is a vectorvalued function called the *drift* coefficient of $\boldsymbol{x}_t$, and $g(\cdot):\mathbb{R}\to \mathbb{R}$ is a scalar function known as the *diffusion* coefficient of $\boldsymbol{x}_t$.

In essence, the diffusion model is to divide a complex task. If it is difficult for the model to directly generate high-definition images, multi-step prediction can significantly reduce the task difficulty and continuously improve the generation quality. At the same time, adding noise to the image blurs the boundary between the right and wrong answers, making the model easier to train and more diverse in effect.

The success of diffusion models on generative tasks makes it easy to think about applying them to supervised learning. This proposal argues that the difference between a supervised learning model and a generative model lies in the completeness of the conditions and the severity of the results.


There have been some works applying diffusion models to image segmentation, especially medical image segmentation.
Brempong, et al. [^1] attempted to use a single Unet-structured denoising pre-trained model for general-purpose image segmentation. 
Graikos, et al. [^2] attempted to optimize the DDPM model as a plug-and-play conditional model and attempted satellite image segmentation.
Wolleb, et al. [^3] also used the DDPM model to create a conditional diffusion model for medical image segmentation tasks.
MedSegDiff [^4] further optimized the structure of the conditional input of the diffusion model and obtained SOTA on the MRI image segmentation data.

Although the current conditional diffusion model has demonstrated excellent performance, even reaching SOTA in medical image segmentation, the training speed and diffusion step size still need to be improved. State-of-the-art DDIM and DPM-solver only need ten steps in image generation but about 50 steps in supervised learning. At the same time, the successful application of conditional diffusion models is still limited to medical images and has yet to be verified in more complex environmental images. In contrast, the pre-training model with pure Unet-structure has already achieved good prediction results without iterations.

### Objectives

本研究的主要目标是基于当前的扩散框架开发用于监督学习的图像条件扩散模型（ICDM）。具体而言，首先扩散模型在监督学习上的可行性将会被进一步分析。在此基础上，不同的超参数和求解器都会被测试比较其性能。最后，本研究会尝试从工程角度加速扩散模型的采样过程。

The primary objective of this research is to develop an image conditional diffusion model (ICDM) for supervised learning based on the current diffusion framework. The ICDM will be trained to perform image segmentation by predicting a segmentation map for a given input image, conditioned on an additional image containing information about the desired object classes or features.

The original Unet has achieved excellent results in the field of image segmentation. From the perspective of engineering requirements, the multi-step iterative Unet must require higher precision and appropriate calculation speed. For supervised learning, given the condition, we need a step size smaller than 5.

### Methods

To achieve this objective, we will follow the following steps:

1.  Implement the ICDM framework by modifying the DDIM model to accept an additional conditional image input.
2.  Train the ICDM on a dataset of images and corresponding segmentation maps, using the conditional image as additional input.
3.  Evaluate the performance of the ICDM on image segmentation tasks, and compare it to the performance of existing diffusion models and other state-of-the-art image segmentation methods.

The experimental part of this proposal has the following considerations: for the diffusion model itself, this proposal will study the selection of alpha and its impact on accelerated sampling; for applying the diffusion model to supervised learning, we will use the existing diffusion model on supervised learning and re-evaluate the performance of the model, try to use the high-order low-step model DPM-solver, and finally compare the diffusion model with the original Unet structure model.

*用监督学习测试生成模型性能或许是一个好方法。*

#### Fast Sampling

According to the original DDPM model, we have the sampling formula [xxx]. DDIM analyzes the method of accelerated sampling of the diffusion model. For the DDPM model, the following sampling can be performed:
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

#### Parameters Setups

According to the definition of the diffusion model, besides the training parameters, the only changing parameters of the model are the artificially set $\alpha$ and $\beta$. They have many setups, mainly linear, sigmoid and cosine schedulers. $\alpha$ and $\beta$ affect the number of steps of the diffusion model and the variance of the added noise.
![600](../../../../../Attachments/4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Generative%20model/alpha&sigma.png)
When the current step is determined, the longer the model prediction step size is, the more significant the proportion of the prediction result will be, and the noise variance will also increase.

#### High-order

DPM-solver proposes that the one-step sampling process can be improved to a higher-order ODE, which raises the accuracy compared to the first-order DDIM within one step. The high-order solvers for diffusion ODEs is as follows:

$\boldsymbol{x}_{t_{i-1} \rightarrow t_{i}}=\frac{\alpha_{t_{i}}}{\alpha_{t_{i-1}}} \tilde{\boldsymbol{x}}_{t_{i-1}}-\alpha_{t_{i}} \sum_{n=0}^{k-1} \hat{\boldsymbol{\epsilon}}_{\theta}^{(n)}\left(\hat{\boldsymbol{x}}_{\lambda_{t_{i-1}}}, \lambda_{t_{i-1}}\right) \int_{\lambda_{t_{i-1}}}^{\lambda_{t_{i}}} e^{-\lambda} \frac{\left(\lambda-\lambda_{t_{i-1}}\right)^{n}}{n !} \mathrm{d} \lambda+\mathcal{O}\left(h_{i}^{k+1}\right)$

However, it is worth noting that, as a supervised model, the performance of Unet already proves its high-order information processing ability. Hence, the effect of high-order sampling still needs further testing. 

### Expected results

We expect the ICDM to outperform existing diffusion models and other state-of-the-art image segmentation methods on a variety of image datasets, by making use of the conditional images with at the expense of a small amount of time. In particular, we expect the ICDM to be able to accurately segment objects of different classes or with different features, based on the information provided by the conditional image.

### Potential Influence

The research proposal titled "Image Conditional Diffusion Model for Supervised Learning" outlines a proposed study to develop an image processing model based on the Denoising Diffusion Implicit Model (DDIM) framework, with the aim of improving the performance of diffusion models on image segmentation tasks.

Image segmentation is an important problem in computer vision, as it involves dividing an image into different regions or segments based on certain characteristics or features. Accurate image segmentation can be useful for a variety of applications, such as object recognition, medical imaging, and robotics.

The proposed Image Conditional Diffusion Model (ICDM) is designed to incorporate additional information about the desired object classes or features in an image through a conditional image input, in order to improve the model's performance on image segmentation tasks. By allowing the model to make use of this additional information, it is expected that the ICDM will be able to more accurately segment objects of different classes or with different features, compared to existing diffusion models or other state-of-the-art image segmentation methods.

If the ICDM is successful in achieving these goals, it could have significant potential influence in the field of image processing and computer vision. The ability to effectively incorporate additional information into diffusion models could lead to improved performance on a variety of image processing tasks, and could potentially enable the development of new applications that rely on accurate image segmentation.

### Experience

I graduated from Xi'an Jiaotong University with a major in software engineering and am currently studying for a master's degree in computer science at the University of Sheffield.

I learned basic machine learning in the second year of undergraduate study and graph neural network (GNN) and multi-task learning (MTL) in the third year. During analyzing MTL-NAS, a model that combines network architecture search (NAS) in MTL, we found that the searched paths between tasks satisfy a specific probability distribution. The search process will degenerate into a pruning operation. Moreover, the search result will settle at the beginning of training. Therefore, we tried to add an attention mechanism to feature fusion to stable training results. Further analysis showed that attention and early stop significantly save time. In conclusion, improved MTL-NAS solved the problem of what to share and when to share with high efficiency, but each task needs a main branch resulting in a large model size.

My undergraduate graduation project focused on the handwritten digital image recognition task, which can be applied to banknote entry. I chose to predict single digits using an end-to-end image recognition model. However, datasets containing single-digit annotations are lacking in practice, so I annotated the ORAND dataset specifically for this task. Moreover, the overlapping of handwritten digits is the key issue. A three-branch model, classification branch, candidate box prediction branch, and weight prediction branch, was designed to receive the output from the backbone network. Meanwhile, overlapping predictions are filtered by Double Non-maximum Suppression (NMS), which increases recall significantly. Future implementation requires more complex preprocessing and post-processing.

Currently, I'm mainly learning natural language processing, specifically speech recognition. Last year, in conjunction with the course, I tried using fusion models for fraud detection.

In conclusion, the recognition methods I've learned and reinforcement learning are both branches of machine learning, so my knowledge will ensure that I can adapt more quickly when I'm get into reinforcement learning. At the same time, learning in multiple directions can also provide more ideas and options for future research.

### Conclusion

In this proposal, we have presented a novel approach for improving the performance of diffusion models on image segmentation tasks, by incorporating additional information about the desired object classes or features through a conditional image input. We believe that the ICDM framework has the potential to significantly advance the state of the art in image segmentation, and look forward to evaluating its performance on a variety of datasets.



[^1]: Brempong, Emmanuel Asiedu, et al. "Denoising Pretraining for Semantic Segmentation." _Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition_. 2022.
[^2]: Graikos, Alexandros, et al. "Diffusion models as plug-and-play priors." _arXiv preprint arXiv:2206.09012_ (2022).
[^3]: Wolleb, Julia, et al. "Diffusion models for implicit image segmentation ensembles." _International Conference on Medical Imaging with Deep Learning_. PMLR, 2022.
[^4]: Wu, Junde, et al. "MedSegDiff: Medical Image Segmentation with Diffusion Probabilistic Model." _arXiv preprint arXiv:2211.00611_ (2022).