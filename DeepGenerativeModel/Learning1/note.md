# Deep image prior
传统的观点认为，学习过程对于构建良好的图像先验至关重要。然而，本文指出：卷积生成网络（CNN）本身的**结构**——而非通过大数据学习到的权重——就足以捕捉大量的自然图像统计信息。

传统的手工图像恢复方法通常将其转化为一个优化问题：
$$x^{*}=\min_{x}E(x;x_0)+R(x)$$
其中，$E(x;x_0)$ 是**数据保真项**（衡量恢复结果与观测值 $x_0$ 的差异），它与具体的图像恢复任务（如去噪、超分）相关；而 $R(x)$ 是显式定义的正则化项（即先验）。

相比之下，本文作者提出使用**网络架构本身**来取代显式的正则化项，优化目标变为：
$$\theta^{*}=\arg\min_{\theta}E(f_{\theta}(z);x_0)$$
由此得到恢复图像 $x^{*}=f_{\theta^{*}}(z)$。这里的 $z$ 是一个固定的随机噪声向量。作者的核心逻辑是：在给定受损图像 $x_0$ 的情况下，通过设计特定的网络框架（如 U-Net），即可实现一种 **“隐式正则化”**。此时，恢复图像的过程从直接搜索像素空间转变为不断优化网络参数以拟合观测数据的过程。

作者在研究中发现，这种方法在拟合图像时存在显著的“阻抗”差异：网络学习一张自然、规律的图像所需的迭代次数，要远远小于学习随机噪声图像。这一特性为图像修复提供了全新的思路：**直接利用随机网络拟合受损图像，并在网络开始摄取噪声细节之前提前停止迭代**，从而在无需外部数据训练的情况下实现图像修复。

# From Learning models of natural image patches to whole image restoration
如何利用图像分块来解决复杂的图像修复问题？

目标：学习得到图像先验来告诉计算机“什么样的图片是自然的、正常的图片”

In this paper we ask three questions: 
(1) Do patch priors that give high likelihoods yield better patch restoration performance? 
- the higher the likelihood a model gives for a set of patches, the better it is in denoising them when they are corrupted.

(2) Do patch priors that give high likelihoods yield better image restoration performance? 
-  In other words, we wish to find a reconstructed image in which every patch is likely under our prior while keeping the reconstructed image still close to the corrupted image — maximizing the Expected Patch Log Likelihood (EPLL) of the reconstructed image, subject to constraining it to be close to the corrupted image.

## EPLL
$$EPLL_{p}(x)=\sum_i \log p(P_ix)$$
其中$P_i$的作用是把图像中第i个Patch给提取出来，然后计算在先验p下它的概率。

>所以到底是如何计算$p(x)$的？
对p进行建模，传统使用GMM正态分布；深度学习方法直接黑盒拟合；VAE中这一步相当于decoder在做的事情，我们需要在latent空间内让latent vector 接近标准的正态分布。
对于GMM模型：
既然你在读 EPLL 和那篇关于“落叶模型”的论文，理解 **GMM（Gaussian Mixture Model，高斯混合模型）** 是极其重要的。

简单来说，GMM 是一种**概率建模工具**。它认为：**任何复杂的数据分布，都可以看作是由多个“简单的”高斯分布（正态分布）加权组合而成的。**

以下从直观理解、数学逻辑以及它在图像处理中的应用三个维度为你介绍：

---

### 1. 直观理解：它是一个“软分类器”

想象你要对自然图像中的“图像块（Patch）”进行分类：
*   有的块是**纯色**的（平坦区域）；
*   有的块是**垂直边缘**；
*   有的块是**水平边缘**；
*   有的块是**复杂的纹理**。

如果用 K-Means 算法，你会强行把一个块分到其中一类。
但 **GMM** 更聪明：它给每个类别准备了一个“高斯分布（模版）”。当你给它一个新块时，它会说：“这个块有 70% 的概率属于‘垂直边缘’，20% 属于‘纹理’，10% 属于‘平坦区’。”

**GMM 就是这所有可能性的“混合（Mixture）”。**

---

### 2. 数学表达：加权求和

GMM 的概率密度函数公式如下（也就是你在论文里经常看到的那个）：
$$p(x) = \sum_{k=1}^{K} \pi_k \mathcal{N}(x | \mu_k, \Sigma_k)$$

这里有三个核心参数：
1.  **$\pi_k$ (权重/混合系数)**：第 $k$ 个高斯分量在整体中所占的比例。所有 $\pi_k$ 加起来等于 1。
2.  **$\mu_k$ (均值)**：第 $k$ 个分布的中心。在图像块里，它代表某种**典型的特征形态**（比如一个标准的 45 度边缘）。
3.  **$\Sigma_k$ (协方差矩阵)**：描述这个分布的形状和分散程度。它决定了这个分量能“容忍”多少变形和噪声。

---

### 3. 如何训练？—— EM 算法

你可能会问：我怎么知道那 200 个分量的 $\mu$ 和 $\Sigma$ 应该是多少？
实操中，我们用 **EM 算法 (Expectation-Maximization)**。它是一个循环的两步：
*   **E-step (期望步)**：根据当前的参数，算出每个数据点属于每个高斯分量的概率（也就是“谁该对这个点负责”）。
*   **M-step (极大化步)**：根据这些点分配的结果，重新计算每个高斯分量的 $\mu$ 和 $\Sigma$，让它们能更好地拟合分配给它们的点。
*   **循环往复**，直到模型不再变化。

---

最终我们的优化问题就是最小化
$$
f_p(x|y)=\frac{\lambda}{2}||Ax-y||^2-EPLL(x)
$$
其中A是退化矩阵，我们假设它已知。

(3) Can we learn better patch priors?
GMM

# Natural Images,Gaussian Mixtures and Dead Leaves
然后作者们在第二年就开始研究GMM为什么如此有实力了。

## GMMs as generative models
对于一个已经解好的GMMs,取其第k个分量，对应的协方差矩阵为$\Sigma_k=V_kD_kV_k^T$。从标准正态分布中随机采样z。
最终生成的图像可以表示为
$$
x=V_kD_k^{0.5}z
$$
其中$V_k$代表某种特征，$D_k$代表这个高斯组成部分学到的对应特征向量应该有的权重。

## Dead leaves models
图像不是像素的堆砌，而是物体的叠加与遮挡模型，并不是完全连续平滑的。
作者在dead leaves model基础上提出了mini dead leaves。

对于每个patch，随机决定是哪种类型的patch。
- flat patch
- edge patch

在《Natural Images, Gaussian Mixtures and Dead Leaves》这篇论文的第 4 节中，作者提出了一个名为 **"Mini Dead Leaves"** 的生成模型。这个模型模拟了如何通过物理遮挡逻辑来构造一个 $8 \times 8$ 的图像块。

生成过程分为两个路径：**生成平坦块（Flat Patch）**和**生成边缘块（Edge Patch）**。

---

### 1. Flat Patch（平坦块）的生成
平坦块代表窗口完全落在某一个物体（贴纸）的内部。它的生成逻辑类似于 **GSM（高斯尺度混合）模型**。

**生成步骤：**
1.  **取纹理基底 ($t$)**：从一个多维高斯分布 $N(0, \Sigma)$ 中采样一个向量 $t$。这里的 $\Sigma$ 是一个**平稳纹理（Stationary Texture）**的协方差矩阵，它的特征向量就是铺满全块的 **Fourier-like（类傅里叶）条纹**。
2.  **加对比度缩放 ($z$)**：随机采样一个标量权重 $z$（代表对比度）。将纹理乘以 $z$（即 $zt$）。这决定了纹理是“清晰可见”还是“模糊暗淡”。
3.  **加平均亮度 ($\mu$)**：随机采样一个标量 $\mu$（DC分量），加到所有像素上。这决定了这块“贴纸”本身的深浅。

**公式表达：**
$$x_{flat} = \mu + zt$$
**视觉效果：** 整个块内呈现出统一的纹理，没有突变的线条。

---

### 2. Edge Patch（边缘块）的生成
边缘块代表窗口跨越了两个不同物体的边界。这是这篇论文最核心的创新点，体现了**“独立遮挡”**。

**生成步骤：**
1.  **准备两个独立的平坦块 ($f$ 和 $g$)**：
    按照上面的方法生成两个完全独立的平坦块 $f$ 和 $g$。你可以把它们想象成两张不同纹理、不同亮度的贴纸。
2.  **生成遮挡掩码（Occlusion Mask, $M$）**：
    *   随机选择一个角度 $\theta$。
    *   随机选择一个距离中心的位置 $r$。
    *   这组 $(\theta, r)$ 在 $8 \times 8$ 的空间里划出了一道**直线边缘**。
    *   这条线将窗口分成了两个区域：$L_1$（左/上区域）和 $L_2$（右/下区域）。
3.  **拼合（Combination）**：
    *   在 $L_1$ 区域的像素，全部取自贴纸 $f$。
    *   在 $L_2$ 区域的像素，全部取自贴纸 $g$。

**公式表达：**
对于像素 $i$，其值 $x_i$ 为：
$$x_i = \begin{cases} f_i & \text{if } i \in L_1 \\ g_i & \text{if } i \in L_2 \end{cases}$$

**视觉效果：** 图像块被一条直线切开，一边是一种纹理，另一边是另一种完全无关的纹理。

---

### 3. 这套生成逻辑说明了什么？

作者通过这种手动生成的方式，向我们揭示了为什么 GMM 会表现出那些特征：

*   **对于 Flat Patch**：因为整个块由同一个 $\Sigma$ 控制，所以它的特征向量（Eigenvectors）必然是**铺满全屏的正弦波（Fourier-like）**。这对应了 GMM 中那些处理平滑区和纯纹理的分量。
*   **对于 Edge Patch**：关键点在于 $f$ 和 $g$ 是**独立采样**的。这意味着左边的像素和右边的像素**没有任何相关性（Uncorrelated）**。
    *   在数学上，这意味着协方差矩阵 $\Sigma$ 在跨越边缘时会出现大量的 **0**（因为相关性为 0）。
    *   当你对这种带 0 的矩阵做特征分解时，算出来的特征向量就会**“缩”在某一边**——也就是你之前看到的“左边有波纹、右边全黑”的局部化结构。

### 总结
*   **Flat Patch** = 1 个纹理过程 + 亮度偏移。
*   **Edge Patch** = 2 个独立的平坦块 + 1 个随机生成的切割面。

**作者的结论是：** 自然图像的统计特性其实非常简单，就是由这两种情况组成的。既然 GMM 能够通过不同的高斯分量分别模拟出这两种生成路径，它当然能在修复图像时表现得如此出色。

使用Mini dead leave手动生成的一系列patch居然和GMM拟合的结果是一样的，说明GMM不是乱打的奥，是有规律的。
这说明自然图像本质其实就是对比度缩放和物体遮挡。