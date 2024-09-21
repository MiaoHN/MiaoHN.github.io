---
title: "Paper | Mask Matching Transformer for Few-Shot Segmentation"
date: 2023-12-06



categories: ["Paper Reading"]
math: true
tags: ["paper", "segmentation"]
---

- 链接：[论文](https://arxiv.org/abs/2301.01208)，[Github](https://github.com/Picsart-AI-Research/Mask-Matching-Transformer)

## TL;DR

文介绍了一种名为 Mask Matching Transformer 的方法，用于解决少样本语义分割问题。该方法通过将分割和匹配模块的学习解耦，采用少对少的匹配范式，提高了分割性能。具体而言，该方法包括三个模块：特征提取模块、潜在物体分割器和掩码匹配模块。特征提取模块使用 ResNet 提取图像特征，潜在物体分割器生成多个掩码提案，掩码匹配模块通过特征对齐和学习匹配来选择最佳掩码。实验结果表明，该方法在几个流行的少样本分割数据集上取得了优异的性能

## 1. Introduction

语义分割是计算机视觉中比较基础的一个任务，近年来发展很快。但由于数据样本存在长尾分布的现象，即绝大多数类别的样本数量很少，从而引出了少样本语义分割（Few-shot segmentation）这个研究方向。当前主流的少样本分割方法的思路主要是找到一种方法有效地利用带标记样本（称为 support，即支持样本）提供的信息来分割测试（称为 query，即查询）图像

**早期的工作**大多遵循“few-to-many”的匹配范式，因为 support 原型的数量通常比 query 特征图中的像素少得多（可能会少 2~3 个数量级）。由于在提取原型时会有信息的损失，这种少对多匹配范式在分割性能方面会受到一定的限制。基于此，**近期的工作**开始探索“many-to-many”的匹配范式。具体来说，support 和 query 特征图之间通过注意力机制或 4D 卷积建模像素级的关系。受益于这些先进的技术，这种多对多的匹配机制相对于少对多的匹配方式表现出优异的性能

> 这些方法的共同点是将匹配操作与分割模块相结合，并将其进行联合优化。不过，这种联合学习的方式不仅**大大增加了学习复杂度**，而且使得**在少样本分割中难以区分匹配模块的效果**
{: .prompt-tip }

![Camparison](/assets/images/2023-12-06-mask-matching-transformer-for-few-shot-segmentation/2023-12-05-09-29-17.png)

在这篇文章中，作者提出了一种不同的视角：**⭐ 将分割和匹配模块的学习解耦 ⭐**（如上图右侧），support 样本并不与像素级 query 特征进行匹配，而是直接与一些与类无关的 query 掩码 proposals 进行匹配，形成“few-to-few”匹配范式。在掩码阶段进行匹配，会有如下几个优点：

1. 这种少对少的匹配范式将匹配从分割模块中解脱出来，使得匹配的针对性更高
2. 降低训练复杂度，因此简单的少对少匹配足以解决 few-shot 分割问题
3. 虽然之前的工作在使用高级特征来匹配和预测分割 mask 时可能会有过拟合的现象，而本文的匹配和分割模块在学习时不会相互影响，从而避免了这个问题

> 文章提到有三篇文献出现了 over-fitting 的现象，这里简单查阅这些文章并给出简单的上下文：
>
> - 第一篇[^1]：模型在训练过程中依赖于由可学习的高级特征产生的准确先验掩码来定位基础类别的目标区域，因此在推理过程中很难推广到以前未见过的类别
> - 第二篇[^2]：embedding space 中具有相同的 prototypes
> - 第三篇[^3]：cycle-consistency 可能会过度抑制支持图像中的正区域
{: .prompt-info }

为了实现这个 few-to-few 的匹配范式，文章设计了一个**两阶段**的框架，并取名为 **Mask Matching Transformer**（即 MM-Former），该框架的大体流程如下：

1. 第一阶段为 query 图像生成 mask proposal，给定从 support 和 query 样本中提取的特征，mask 匹配模块通过 mask 全局平均池化从 support 和 query 特征 中获取原型
2. 第二阶段将 support 样本与 mask proposal 进行匹配，并为每个候选 query 生成一组系数
3. 最后根据系数将 mask porposal 进行组合来得到这个 support-query 对的最终分割结果

由于所有的图片都是经过一个固定的网络得到特征，它们在特征空间可能不会对齐得很好，同时在特征空间中还需要处理新类的情况。所以这里提出了一个 Feature Alignment Block 对特征进行处理，从而在后面的 matching 操作中放心地使用这些特征

## 2. Methodology

直接放图：

![Overview](/assets/images/2023-12-06-mask-matching-transformer-for-few-shot-segmentation/2023-12-05-10-22-44.png)

整个模型大体可以分为 3 个部分：

- 主干网络（Backbone Network）：仅用于提取特征，其参数在训练过程中固定不变
- 潜在物体分割器（Potential Objects Segmenter）：简称 POS，用于生成多个 mask proposal，这些 proposal 可能会包括包含给定图像在内的潜在对象区域
- 掩码匹配模块（Mask Matching Module）：简称 MM，以 support 的线索为指导，从 mask proposal 中选择最有可能的一个

选定的 mask 会最终合并到目标输出中。下面对这三个模块进行一个详细的解释

### 2.1 Feature Extraction Module

该模块使用 ResNet 来提取输入图像的特征。与先前的一些方法有所不同，这里保留了 ResNet 的原始结构[^5]。后续模块会使用 ResNet 最后三层的输出，这里将其命名为图片 $I_S$ 和 $I_Q$ 的 $F_S$ 和 $F_Q$，其中 $F=\\{F^i\\},i\in[3,4,5]$，$F$ 是 $I_S$ 或 $I_Q$ 的特征，$i$ 是 backbone 的层索引。之后会提取 query 的 layer-2 的输出来获得分割 mask（将其命名为 $F_Q^2$）。$F^2,F^3,F^4,F^5$ 相对于输入图像的步长为 $\\{4,8,16,32\\}$

### 2.2 Potential Objects Segmenter

POS 的目的是分割一张图像中的所有对象。参考 Mask2Former，使用一个标准的 transformer decoder 计算 $F_S$ 和 $N$ 个可学习 embeddings 之间的交叉注意力。transformer decoder 由 3 个连续的 transformer layer 组成，每个 layer 都将相应的 $F^i$ 作为输入。transformer decoder 的每一层都可表示为：

$$
E^{l+1}=\text{TLayer}^l(E^l,F^i)
$$

这里 $E^{l}$ 和 $E^{l+1}$ 分别表示经过 transformer 之前和之后的 $N$ 个可学习 embeddings。transformer decoder 的输出与 $F^2_Q$ 相乘来得到 $N$ 个 mask proposals：$M\in\mathcal{R}^{N\times H/4\times W/4}$。注意这里用 `Sigmoid` 将所有的 mask proposals 标准化为 $[0,1]$ 的值。此外，POS 省略了 Mask2Former 的分类器，因为这里不需要对 mask proposal 进行分类

### 2.3 Mask Matching (MM) Module

MM 模块由一个 Feature Alignment block 和一个 Learnable Matching block 组成。首先使用 Feature Alignment block 从像素级对 $F_Q$ 和 $F_S$ 进行对齐。然后，在 Learnable Matching block 中将对应于 support 图像的适当的 query mask 进行匹配

**_Feature Alignment Block_**

![Feature Alignment Block](/assets/images/2023-12-06-mask-matching-transformer-for-few-shot-segmentation/2023-12-05-11-18-37.png)

Feature Alignment Block 由 Self-Alignment block 和 Cross-Alignment block 这两个结构组成。受极化自注意力[^16]的启发，这里设计了一个非参数化的 block 来将输入进行标准化。简单来说，通过对不同的 channel 进行平均并加权，得到的 $A$ 即是每个通道的权重。通过这种方式，输入特征在 channel 这个维度上进行调整，并且异常值应该会被平滑。下图是一个 3 通道的例子，从颜色上看经过 FAB 的结果会更加柔和

![result](/assets/images/2023-12-06-mask-matching-transformer-for-few-shot-segmentation/result.svg)

引入 Cross-Alignment block 是为了减轻不同图像之间的差异。$F_S$ 和 $F_Q$ 同时被送入两个共享权重的 transformer decoder 中。以第 $i$ 层为例，该过程可以表示为：

$$
\begin{aligned}
\hat{F_Q^i}=&\text{MLP}(\text{MHAtten}(F_Q^i),F_S^i,F_S^i)\\\\
\hat{F_S^i}=&\text{MLP}(\text{MHAtten}(F_S^i),F_Q^i,F_Q^i)
\end{aligned}
$$

其中 $F_Q^i$ 和 $F_S^i$ 表示 $F_Q$ 和 $F_S$ 的第 $i$ 层特征，这里 $\hat{F}=\\{\hat{F}^i\\}_i^L,i\in[3,4,5]$

**_Learnable Matching Block_**

获取 $\hat{F}_S$ 和 $\hat{F}_Q$ 后，首先对每个 $\hat{F}^i$ 应用带 mask 的全局平均池化（GAP），并将它们连接在一起生成 support ground-truth 和 $N$ 个 query mask proposals，并命名为 $\\{P_S^{gt}\\},\\{P_Q^i\\},P_S^{gt},P_Q^n\in\mathbb{R}^{3d}$，这里 $n\in[1,2,\dots,N]$。其中 $d$ 是 $\hat{F}^i$ 的维度，$3d$ 是由 $\\{\hat{F}^3,\hat{F}^4,\hat{F}^5\\}$ 连接在一起得到的。文章使用余弦相似度来衡量原型 $P_S^{gt}$ 和 $P_Q^n$ 之间的相似度。有时候相似度最高的原型对应的 mask 可能不完整（比如 support 图像只有对象的一部分），所以进一步使用 MLP 来合并相应的 mask。详细过程可写为：

$$
\begin{aligned}
  S=&\cos(P_S^{gt},P_Q^n),n\in[1,2,\dots,N],\\\\
  \hat{M}=&M\times\text{MLP}(S),S\in R^{1\times N}
\end{aligned}
$$

### 2.4 Training Strategy

为了避免训练过程中 POS 和 MM 模块的相互影响，文章提出了先训练 POS，再训练 MM 的两阶段训练策略。此外，通过解耦 POS 和 MM，网络可以在 1-shot 和 K-shot 设置下共享相同的 POS，这大大提高了训练效率

## 3. Experiments

本文在两个流行的 few-shot 分割数据集 Pascal-5i 和 COCO-20i 上进行实验，并使用平均交并比（mIoU）作为实验的评估指标

![Table 1](/assets/images/2023-12-06-mask-matching-transformer-for-few-shot-segmentation/2023-12-06-10-20-03.png)

![Table 2](/assets/images/2023-12-06-mask-matching-transformer-for-few-shot-segmentation/2023-12-06-10-20-59.png)

## 4. Conclusion

**_Limitations and societal impact_**：MM-Former 在 few-shot 分割的研究领域中引入了先分解再融合的范式，这是一个全新的视角，可能会启发未来的研究人员开发更先进的版本。但目前的结果与预言（oracle）仍有较大差距（≈20% mIoU）。如何进一步缩小这一差距是未来可能的研究重点

---

[^1]: Z Tian, H Zhao, M Shu, Z Yang, R Li, and J Jia. Prior guided feature enrichment network for few-shot segmentation. IEEE Transactions on Pattern Analysis and Machine Intelligence, 2020.

[^2]: Kaixin Wang, Jun Hao Liew, Yingtian Zou, Daquan Zhou, and Jiashi Feng. Panet: Few-shot image semantic segmentation with prototype alignment. In Proceedings of the IEEE/CVF International Conference on Computer Vision, pages 9197–9206, 2019.

[^3]: Gengwei Zhang, Guoliang Kang, Yi Yang, and Yunchao Wei. Few-shot segmentation via cycle-consistent transformer. Advances in Neural Information Processing Systems, 34, 2021.

[^5]: Bowen Cheng, Alex Schwing, and Alexander Kirillov. Per-pixel classification is not all you need for semantic segmentation. Advances in Neural Information Processing Systems, 34, 2021.

[^16]: Huajun Liu, Fuqiang Liu, Xinyi Fan, and Dong Huang. Polarized self-attention: Towards high-quality pixel-wise regression. arXiv preprint arXiv:2107.00782, 2021.
