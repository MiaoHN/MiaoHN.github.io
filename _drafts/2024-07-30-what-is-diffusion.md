---
title: "Diffusion 原理探究"
date: 2024-07-29T10:10:10+08:00
description: "学习 Diffusion 的原理"

math: true
tags: ["diffusion"]
---

![allegory_of_the_cave](https://upload.wikimedia.org/wikipedia/commons/thumb/8/8d/An_Illustration_of_The_Allegory_of_the_Cave%2C_from_Plato%E2%80%99s_Republic.jpg/435px-An_Illustration_of_The_Allegory_of_the_Cave%2C_from_Plato%E2%80%99s_Republic.jpg)

> 在洞喻中，柏拉图描述了一群人，他们一生都被锁链拴在洞穴的墙壁旁，面对着一堵空白的墙壁。这些人观察从他们身后火堆前经过的物体投射在墙上的影子，并为这些影子命名。影子是囚犯们的现实，但并不是真实世界的准确呈现。
>
> -- [地穴寓言](https://en.wikipedia.org/wiki/Allegory_of_the_cave)

## 引子

与地穴寓言类似，对于许多模态的数据而言（诸如文本、图像），我们都可以将观测到的数据 $\boldsymbol{x}$ 的概率分布 $p(\boldsymbol{x})$ 映射到一个潜空间，并使用一个随机变量 $z$ 来表示这个映射。这种映射其实在生活中很常见，我们也经常对物体的颜色、大小、形状等属性进行抽象，并通过这种抽象对物体的形状进行一个概括。对洞里的人来说，他们永远都不太可能完全理解影子的全部属性，但仍可以对其进行推理和推断。

> ~~我们永远的无法真正了解事物的本质~~
{: .prompt-tip }

其实在生成模型中同样如此，<mark>大部分的生成模型倾向于学习低维的潜变量，这也可以视作一种信息的压缩</mark>，并可能会不自觉地揭示一些对描述观察结果有意义的结构。

## 推导

在数学上可以假设潜在变量 $z$ 和观察到的数据 $\boldsymbol{x}$ 服从某种概率分布 $p(\boldsymbol{x},z)$。为了得到 $\boldsymbol{x}$ 的概率分布，我们可以对 $z$ 进行[边缘似然](https://en.wikipedia.org/wiki/Marginal_likelihood)：

$$
\begin{align}
p(\boldsymbol{x})=\int p(\boldsymbol{x},\boldsymbol{z})d\boldsymbol{z},
\end{align}
$$

或者，也可以使用概率的[链式法则](https://en.wikipedia.org/wiki/Chain_rule_(probability))：

$$
\begin{align}
p(\boldsymbol{x})=\frac{p(\boldsymbol{x},\boldsymbol{z})}{p(\boldsymbol{z}|\boldsymbol{x})}.
\end{align}
$$

### ELBO 是什么

直接求解这个 $p(\boldsymbol{x})$ 有亿点点难，毕竟有个潜变量 $\boldsymbol{z}$ 很难搞。不过通过这两个式子，可以推导出一个叫做 **E**vidence **L**ower **Bo**und (ELBO) 的术语：

$$
\begin{align}
\mathbb{E}_{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\left[\log\frac{p(\boldsymbol{x},\boldsymbol{z})}{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\right] (\leq\log{p(\boldsymbol{x})}).
\end{align}
$$

这里 $q_\phi(\boldsymbol{z}|\boldsymbol{x})$ 是一个灵活的近似变分分布，其参数 $\phi$ 为我们需要优化的变量。这个分布可以用于估计真实的后验分布 $p(\boldsymbol{z}|\boldsymbol{x})$

> As we will see when exploring the Variational Autoencoder, as we increase the lower bound by tuning the parameters φ to maximize the ELBO, we gain access to components that can be used to model the true data distribution and sample from it, thus learning a generative model.

下面，让我们继续深入探讨为什么要最大化 ELBO。首先对公式 1 和 2 进行适当的变形，就可以得到：

$$
\begin{align}
  \log p(\boldsymbol{x})
  &=\log\int p(\boldsymbol{x},\boldsymbol{z})d\boldsymbol{z}\\\\
  &=\log\int\frac{p(\boldsymbol{x},\boldsymbol{z})q\_\phi(\boldsymbol{z}|\boldsymbol{x})}{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}d\boldsymbol{z}\\\\
  &=\log\mathbb{E}\_{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\left[\log\frac{p(\boldsymbol{x},\boldsymbol{z})}{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\right]\\\\
  &\geq\mathbb{E}\_{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\left[\log\frac{p(\boldsymbol{x},\boldsymbol{z})}{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\right].
\end{align}
$$

其实从这里已经可以得到 ELBO，但因为使用了 [Jensen's Inequality](https://en.wikipedia.org/wiki/Jensen%27s_inequality)，最后的这个不等式并不能提供更多的信息，无法说明 ELBO 确实是一个下界，也不能真正告诉我们为什么要将最大化它作为目标。为了方便理解 ELBO 和实际的关系，下面从另一个角度进行推导：

$$
\begin{align}
  \log p(\boldsymbol{x})
  &=\log p(\boldsymbol{x})\int q\_\phi(\boldsymbol{z}|\boldsymbol{x})dz\\\\
  &=\int q\_\phi(\boldsymbol{z}|\boldsymbol{x})(\log p(\boldsymbol{x}))dz\\\\
  &=\mathbb{E}\_{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\left[\log p(\boldsymbol{x})\right]\\\\
  &=\mathbb{E}\_{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\left[\log \frac{p(\boldsymbol{x},\boldsymbol{z})}{p(\boldsymbol{z}|\boldsymbol{x})}\right]\\\\
  &=\mathbb{E}\_{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\left[\log \frac{p(\boldsymbol{x},\boldsymbol{z})q\_\phi(\boldsymbol{z}|\boldsymbol{x})}{p(\boldsymbol{z}|\boldsymbol{x})q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\right]\\\\
  &=\mathbb{E}\_{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\left[\log \frac{p(\boldsymbol{x},\boldsymbol{z})}{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\right]+\mathbb{E}\_{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\left[\log\frac{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}{p(\boldsymbol{z}|\boldsymbol{x})}\right]\\\\
  &=\mathbb{E}\_{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\left[\log \frac{p(\boldsymbol{x},\boldsymbol{z})}{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\right]+D\_\text{KL}(q\_\phi(\boldsymbol{z}|\boldsymbol{x})||p(\boldsymbol{z}|\boldsymbol{x}))\\\\
  &\geq\mathbb{E}\_{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\left[\log\frac{p(\boldsymbol{x},\boldsymbol{z})}{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\right].
\end{align}
$$

从这里可以看出，<mark>真实的分布就等于 ELBO 加上近似后验 $q\_\phi(\boldsymbol{z}|\boldsymbol{x})$ 与真实后验 $p(\boldsymbol{z}|\boldsymbol{x})$ 之间的 KL 散度</mark>。

> **the difference between the evidence and the ELBO is a strictly non-negative KL term, thus the value of the ELBO can never exceed the evidence.**

然后，简单讨论一下为什么我们要最大化 ELBO。引入潜变量 $\boldsymbol{z}$ 后，我们想要学习描述观察数据的潜在结构，即优化变分后验 $q\_\phi(\boldsymbol{z}|\boldsymbol{x})$ 的参数，来匹配真实的后验分布 $p(\boldsymbol{z}|\boldsymbol{x})$，理想情况下 KL 散度应该为 0。但是，直接最小化 KL 散度很困难，但在公式 14 中可以发现最终的 $\log p(\boldsymbol{x})$ 并不依赖于 $\phi$。这样来看，最大化 ELBO 也就相当于最小化 KL 散度。因此，<mark>对 ELBO 优化得更多，KL 散度更小，近似后验也越接近与真实后验</mark>。

### 变分自编码器（VAE）

![VAE](https://ar5iv.labs.arxiv.org/html/2208.11970/assets/images/vae.png)
_A Variational Autoencoder graphically represented._

在 VAE（Variational Autoencoder）的基本处理中，直接最大化 ELBO。这种方式是 _变分（variational）_ 的，因为我们是在由 $\phi$ 参数化的一系列潜在后验分布中优化最佳的 $q\_\phi(\boldsymbol{z}|\boldsymbol{x})$。让我们继续推导来弄清这些关系：

$$
\begin{align}
  \mathbb{E}\_{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\left[\log\frac{p(\boldsymbol{x},\boldsymbol{z})}{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\right]
  &=\mathbb{E}\_{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\left[\log\frac{p\_\theta(\boldsymbol{x}|\boldsymbol{z})p(\boldsymbol{z})}{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\right]\\\\
  &=\mathbb{E}\_{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\left[\log p\_\theta(\boldsymbol{x}|\boldsymbol{z})\right]+\mathbb{E}\_{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\left[\log\frac{p(\boldsymbol{z})}{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\right]\\\\
  &=\underbrace{\mathbb{E}\_{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\left[\log p\_\theta(\boldsymbol{x}|\boldsymbol{z})\right]}\_\text{reconstruction term}-\underbrace{D\_\text{KL}(q\_\phi(\boldsymbol{z}|\boldsymbol{x})||p(\boldsymbol{z}))}\_{\text{prior matching term}}\\\\
\end{align}
$$

在这个情况下，我们学习了一个中间的瓶颈分布 $q\_\phi(\boldsymbol{z}|\boldsymbol{x})$，它将输入转换为可能的潜变量的分布，可以视为编码器；同时，还学习了一个确定性函数 $p\_\theta(\boldsymbol{x}|\boldsymbol{z})$，它将给定的潜向量 $\boldsymbol{z}$ 转换为观测值 $\boldsymbol{x}$，可作为解码器。

公式 18 中的第一项根据变分分布来衡量解码器重构的可能性，第二项衡量学习到的变分分布与潜在变量的先验分布的相似程度。最小化第二项会鼓励编码器学习实际分布，而不是崩溃为 Dirac delta 函数。因此，<mark>最大化 ELBO 相当于最大化第一项并最小化第二项</mark>。

VAE 需要考虑如何利用参数 $\phi$ 和 $\theta$ 共同优化 ELBO。VAE 的编码器通常选择具有对角协方差的多元高斯进行建模，同时先验通常被选为标准多元高斯：

$$
\begin{align}
  q\_\phi(\boldsymbol{z}|\boldsymbol{x})&=\mathcal{N}(\boldsymbol{z};\boldsymbol{\mu}\_\phi(\boldsymbol{x}),\boldsymbol{\sigma}\_\phi^2(\boldsymbol{x})\boldsymbol{I})\\\\
  p(\boldsymbol{z})&=\mathcal{N}(\boldsymbol{z};\boldsymbol{0},\boldsymbol{I})
\end{align}
$$

接下来，就可以对 ELBO 的 KL 散度进行分析计算，并可以使用蒙特卡洛估计来近似重建项。

$$
\argmax\_{\boldsymbol{\phi,\theta}}\mathbb{E}\_{q\_\phi(\boldsymbol{z}|\boldsymbol{x})}\left[\log p\_\theta(\boldsymbol{x}|\boldsymbol{z})\right]-D\_\text{KL}(q\_\phi(\boldsymbol{z}|\boldsymbol{x})||p(\boldsymbol{z}))\approx\argmax\_{\boldsymbol{\phi,\theta}}\sum\_{l=1}^L\log p\_\theta(\boldsymbol{x}|\boldsymbol{z^{(l)}})-D\_\text{KL}(q\_\phi(\boldsymbol{z}|\boldsymbol{x})||p(\boldsymbol{z}))
$$

因为在计算过程中每个 $\boldsymbol{z}^{(l)}$ 都是随机采样生成的，通常不可微分。不过这个问题可以通过重新参数化技巧（reparameterization trick）进行解决，它将随机变量写为带有噪声变量的确定性函数。比如，来自具有任意均值 $\mu$ 和方差 $\sigma^2$ 的正态分布 $x\sim\mathcal{N}(x;\mu,\sigma^2)$ 的样本而言重写为：

$$
x=\mu+\sigma\epsilon\quad\text{with }\epsilon\sim\mathcal{N}(\epsilon,0,I)
$$

因此，可以通过从标准高斯分布采样，按目标标准差缩放结果并按目标均值平移来执行任意高斯分布的采样。在 VAE 中，每一个 $\boldsymbol{z}$ 都可以被输入 $\boldsymbol{x}$ 和辅助噪声变量 $\boldsymbol{\epsilon}$ 表示为一个确定性函数：

$$
z=\boldsymbol{\mu}\_\phi(\boldsymbol{x})+\boldsymbol{\sigma}\_\phi(\boldsymbol{x})\odot\epsilon\quad\text{with }\boldsymbol\epsilon\sim\mathcal{N}(\boldsymbol\epsilon,\boldsymbol0,\boldsymbol I)
$$

训练好 VAE 后，<mark>可以通过直接从潜在空间 $p(\boldsymbol{z})$ 采样然后通过解码器运行来生成新数据</mark>。当 $\boldsymbol{z}$ 的维度小于输入 $\boldsymbol{x}$ 的维度时，变分自动编码器特别有趣，因为我们可能会学习紧凑、有用的表示。此外，当学习到语义上有意义的潜在空间时，可以在将潜在向量传递到解码器之前对其进行编辑，以更精确地控制生成的数据。

### 分层变分自动编码器（HVAE）

![HVAE](https://ar5iv.labs.arxiv.org/html/2208.11970/assets/images/hvae.png)
_A Markovian Hierarchical Variational Autoencoder._

分层变分自动编码器（HVAE）是 VAE 的泛化形式，可以扩展到多个潜变量。这里

## 参考
