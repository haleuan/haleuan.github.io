+++
title = 'Paper dive | Simple is effective'
date = 2025-04-17T01:19:16Z
draft = false
comments = false
+++
## Metadata
**Title**: [Simple Is Effective: The Roles of Graphs and Large Language Models in Knowledge-Graph-Based Retrieval-Augmented Generation](https://arxiv.org/abs/2410.20724)
**Venue**: ICLR 2025
**Source Code**: [Github](https://github.com/Graph-COM/SubgraphRAG)


## TL;DR
### Motivation：
1. Retriever效率和准确性之间的平衡。
2. 知识的结构信息没有被充分利用

---

## Section Notes
### The SubgraphRAG Framework
#### 图的建立
- 
> Given a training set of question-answer pairs D, the subgraph retriever learning problem can be formulated as the following problem:$$\max_\theta\mathbb{E}_{(q,\mathcal{A}_q)\thicksim\mathcal{D},\mathcal{G}_q\thicksim\mathbb{Q}_\theta(\mathcal{G}_q|q,\mathcal{G})}\mathbb{P}(\mathcal{A}_q\mid\mathcal{G}_q,q).$$

对于 $\mathbb{E}_{(q,\mathcal{A}_q)\thicksim\mathcal{D},\mathcal{G}_q\thicksim\mathbb{Q}_\theta(\mathcal{G}_q|q,\mathcal{G})}\mathbb{P}(\mathcal{A}_q\mid\mathcal{G}_q,q)$，其中 $\mathcal{G}_q\thicksim\mathbb{Q}_\theta(\mathcal{G}_q|q,\mathcal{G})$ 表示 $\mathcal{G}_q$ 从条件分布 $\mathbb{Q}_\theta(\mathcal{G}_q \mid q, \mathcal{G})$ 中采样,即模型以参数 $\theta$ 控制生成过程，输出 $\mathcal{G}_q$（例如推理路径、中间结构等）。所以就是要控制 $\theta$，使得该期望最大。

- 
> This problem is hard to solve due to the complexity of the LLM ($\mathbb P$).

这里的意思是说，大模型输出 $\mathcal{A}_q$ 的概率（即，$\mathbb{P}(\mathcal{A}_q\mid\mathcal{G}_q,q)$）无法计算，$\frac{d\mathbb{P}}{d\mathcal{G}_q}$ 也就更无法计算了。要使 $\mathbb{P}(\mathcal{A}_q\mid\mathcal{G}_q,q)$ 最大，可以这样想：
在机器学习中，我们要使损失函数 $L$ 最小，是一个求最值的问题（虽然实际上很多情况下求出来的是一个极值）的过程。同理，使 $\mathbb P$ 最大，也是一个求最值的问题，当然，$\mathbb P$ 是需要有上界的，这里的 $\mathbb P$ 是表示概率，所以必定是有上界的。

- 
> To solve the problem in Eq. 1, we adopt the following strategy.$$\max_\theta\mathbb{E}_{(q,\mathcal{A}_q)\sim\mathcal{D},\mathcal{G}_q\sim\mathbb{Q}_\theta(\mathcal{G}_q|q,\mathcal{G})}\mathbb{P}(\mathcal{A}_q\mid\mathcal{G}_q,q).$$

既然我们无法优化LLM的输出，那么我们可以优化检索的信息，也就是找到的信息实体图中的最佳子图。*既然不能优化回答器（LLM），那我们就优化它的输入，也就是“给它看的那部分知识图谱”*。

- 
> However, getting $\mathcal{G}_q^∗$ for an even known question-answer pair $(q,\mathcal{A}_q)$ is computationally hard and LLM-dependent.Instead, we use (q, Aq) to construct surrogate subgraph evidence with heuristics $\tilde{\mathcal{G}}(q,\mathcal{A}_q)$ and train the retriever based on MLE: $$\max_\theta\mathbb{E}_{(q,\mathcal{A}_q)\thicksim\mathcal{D}}\mathbb{Q}_\theta(\tilde{\mathcal{G}}_q\mid\mathcal{G},q),\quad\mathrm{where~}\tilde{\mathcal{G}}_q=\tilde{\mathcal{G}}(q,\mathcal{A}_q).$$

但是即使有了($q$, $\mathcal{A}_q$)，怎么得出最佳子图 $\mathcal{G}_q^*$ 仍然是个问题，并且该问题还依赖于LLM。所以文中采用**启发式训练**，将 $(q,\mathcal{A}_q)$ 作为训练资料，利用MLE训练检索器：$$\max_\theta\mathbb{E}_{(q,\mathcal{A}_q)\thicksim\mathcal{D}}\mathbb{Q}_\theta(\tilde{\mathcal{G}}_q\mid\mathcal{G},q),\quad\mathrm{where~}\tilde{\mathcal{G}}_q=\tilde{\mathcal{G}}(q,\mathcal{A}_q).\qquad (2)$$
理解**启发式训练**：所谓的启发式，就是预先人为定义一个 $\tilde{\mathcal{G}}$（文中举了一个例子，$\mathcal T_q$ 和 $\mathcal A_q$ 之间所有最短路组成的子图），这里的 $\tilde{\mathcal{G}}$ 充当了一个伪标签的角色。


> Triple Factorization We propose to adopt a retriever that allows a subgraph distribution factorization over triples given some latent variables $z_\tau=z_\tau(\mathcal{G},q)$ (to be elaborated later):$$\mathbb{Q}_\theta(\mathcal{G}_q\mid\mathcal{G},q)=\prod_{\tau\in\mathcal{G}_q}p_\theta(\tau\mid z_\tau,q)\prod_{\tau\in\mathcal{G}\setminus\mathcal{G}_q}\left(1-p_\theta\left(\tau\mid z_\tau,q\right)\right).\qquad (3)$$

这里的 $z_\tau=z_\tau(\mathcal{G},q)$ 是一个latent variable，用于在建立 $\tilde{\mathcal{G}}_q$ 时，对应的三元组是否被纳入该子图。
公式解读：
$\mathcal{G} \backslash \mathcal{G}_q$ 可以看作是 $\mathcal{G}_q$ 在 $\mathcal{G}$ 中的补集（虽然严格来说陪集分解会更复杂一些）。这个公式可以看作是一个**判别模型**，用于判断群 $\mathcal{G}$ 中的每个元素 $\tau$ 是否属于子群 $\mathcal{G}_q$：
- 如果 $\tau \in \mathcal{G}_q$，我们希望 $p_\theta(\tau \mid z_\tau, q)$ 接近 1（高概率）。
- 如果 $\tau \notin \mathcal{G}_q$，我们希望 $p_\theta(\tau \mid z_\tau, q)$ 接近 0（低概率），因此 $1 - p_\theta(\tau \mid z_\tau, q)$ 接近 1。
所以结合 $Eq.2$ 和 $Eq.3$，训练目的就是使得 $\mathbb{Q}_\theta(\mathcal{G}_q\mid\mathcal{G},q)$ 的期望最大。几个变量的关系如下：$$\mathcal{z}_{(h,r,t)}\longrightarrow \mathcal{z}_\tau\longrightarrow p_\theta(\tau\mid z_\tau,q) \longrightarrow \mathbb{Q}_\theta(\mathcal{G}_q\mid\mathcal{G},q)$$
#### Directional Distance Encoding (DDE)
