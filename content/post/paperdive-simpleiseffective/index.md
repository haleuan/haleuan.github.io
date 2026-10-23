+++
ShowToc = true
categories = ['Paper Reading', 'Research Notes']
comments = false
date = '2025-04-17T01:19:16Z'
draft = false
tags = ['RAG', 'Retriever', 'Deep Learning']
title = 'Paper Reading | Simple Is Effective'
+++

## Metadata
**Title**: [Simple Is Effective: The Roles of Graphs and Large Language Models in Knowledge-Graph-Based Retrieval-Augmented Generation](https://arxiv.org/abs/2410.20724)

**Venue**: ICLR 2025

**Source Code**: [GitHub](https://github.com/Graph-COM/SubgraphRAG)

## TL;DR
### Motivation
1. Balancing retriever efficiency and accuracy.
2. Making better use of the structural information in knowledge.

---

## Sections
### The SubgraphRAG Framework
#### Constructing the graph
>
> Given a training set of question-answer pairs D, the subgraph retriever learning problem can be formulated as the following problem:
> $$\max_\theta\mathbb{E}_{(q,\mathcal{A}_q)\thicksim\mathcal{D},\mathcal{G}_q\thicksim\mathbb{Q}_\theta(\mathcal{G}_q|q,\mathcal{G})}\mathbb{P}(\mathcal{A}_q\mid\mathcal{G}_q,q).$$

In the expression $\mathbb{E}_{(q,\mathcal{A}_q)\thicksim\mathcal{D},\mathcal{G}_q\thicksim\mathbb{Q}_\theta(\mathcal{G}_q|q,\mathcal{G})}\mathbb{P}(\mathcal{A}_q\mid\mathcal{G}_q,q)$, the term $\mathcal{G}_q\thicksim\mathbb{Q}_\theta(\mathcal{G}_q \mid q, \mathcal{G})$ means that $\mathcal{G}_q$ is sampled from the conditional distribution $\mathbb{Q}_\theta(\mathcal{G}_q \mid q, \mathcal{G})$. In other words, the model uses parameters $\theta$ to control the generation process and produce $\mathcal{G}_q$ (for example, an inference path or intermediate structure). The goal is to control $\theta$ so that this expectation is maximized.

>
> This problem is hard to solve due to the complexity of the LLM ($\mathbb P$).

This means that the probability of the LLM producing answer $\mathcal{A}_q$ (that is, $\mathbb{P}(\mathcal{A}_q\mid\mathcal{G}_q,q)$) cannot be computed, so neither can $\frac{d\mathbb{P}}{d\mathcal{G}_q}$. To maximize $\mathbb{P}(\mathcal{A}_q\mid\mathcal{G}_q,q)$, consider the analogy with minimizing a loss function $L$ in machine learning: this is an optimization problem (although in practice the result is often a local extremum). Similarly, maximizing $\mathbb P$ is an optimization problem. Here $\mathbb P$ is a probability and therefore has an upper bound.

>
> To solve the problem in Eq. 1, we adopt the following strategy.
> $$\max_\theta\mathbb{E}_{(q,\mathcal{A}_q)\sim\mathcal{D},\mathcal{G}_q\sim\mathbb{Q}_\theta(\mathcal{G}_q|q,\mathcal{G})}\mathbb{P}(\mathcal{A}_q\mid\mathcal{G}_q,q).$$

Since we cannot optimize the LLM's output, we can instead optimize the retrieved information: the best subgraph in the information graph. *If we cannot optimize the answerer (the LLM), we can optimize its input—the part of the knowledge graph it sees.*

>
> However, getting $\mathcal{G}_q^∗$ for an even known question-answer pair $(q,\mathcal{A}_q)$ is computationally hard and LLM-dependent. Instead, we use (q, Aq) to construct surrogate subgraph evidence with heuristics $\tilde{\mathcal{G}}(q,\mathcal{A}_q)$ and train the retriever based on MLE:
> $$\max_\theta\mathbb{E}_{(q,\mathcal{A}_q)\thicksim\mathcal{D}}\mathbb{Q}_\theta(\tilde{\mathcal{G}}_q\mid\mathcal{G},q),\quad\mathrm{where~}\tilde{\mathcal{G}}_q=\tilde{\mathcal{G}}(q,\mathcal{A}_q).$$

Even given $(q, \mathcal{A}_q)$, finding the best subgraph $\mathcal{G}_q^*$ remains a problem, and it also depends on the LLM. The paper therefore uses **heuristic training**, treating $(q,\mathcal{A}_q)$ as training data and training the retriever with MLE:
$$\max_\theta\mathbb{E}_{(q,\mathcal{A}_q)\thicksim\mathcal{D}}\mathbb{Q}_\theta(\tilde{\mathcal{G}}_q\mid\mathcal{G},q),\quad\mathrm{where~}\tilde{\mathcal{G}}_q=\tilde{\mathcal{G}}(q,\mathcal{A}_q).\quad (1)$$
To understand **heuristic training**: a heuristic here means manually defining $\tilde{\mathcal{G}}$ in advance. The paper gives an example in which $\tilde{\mathcal{G}}$ is the subgraph formed by all shortest paths between $\mathcal T_q$ and $\mathcal A_q$. It serves as a pseudo-label.

> **Triple Factorization** We propose to adopt a retriever that allows a subgraph distribution factorization over triples given some latent variables $z_\tau=z_\tau(\mathcal{G},q)$ (to be elaborated later):
> $$\mathbb{Q}_\theta(\mathcal{G}_q\mid\mathcal{G},q)=\prod_{\tau\in\mathcal{G}_q}p_\theta(\tau\mid z_\tau,q)\prod_{\tau\in\mathcal{G}\setminus\mathcal{G}_q}\left(1-p_\theta\left(\tau\mid z_\tau,q\right)\right).\quad (2)$$

Here $z_\tau=z_\tau(\mathcal{G},q)$ is a latent variable used to determine whether the corresponding triple is included in the subgraph $\tilde{\mathcal{G}}_q$.
Interpreting the formula: $\mathcal{G} \backslash \mathcal{G}_q$ can be viewed as the complement of $\mathcal{G}_q$ in $\mathcal{G}$ (although the decomposition would be more involved in a strict mathematical treatment). The formula can be viewed as a **discriminative model** that determines whether each element $\tau$ in the set $\mathcal{G}$ belongs to the subset $\mathcal{G}_q$:
- If $\tau \in \mathcal{G}_q$, we want $p_\theta(\tau \mid z_\tau, q)$ to be close to 1.
- If $\tau \notin \mathcal{G}_q$, we want $p_\theta(\tau \mid z_\tau, q)$ to be close to 0, so that $1 - p_\theta(\tau \mid z_\tau, q)$ is close to 1.

Thus, combining Eq. 2 and Eq. 3, the training objective is to maximize the expectation of $\mathbb{Q}_\theta(\mathcal{G}_q\mid\mathcal{G},q)$. The variables are related as follows:
$$\mathcal{z}_{(h,r,t)}\longrightarrow \mathcal{z}_\tau\longrightarrow p_\theta(\tau\mid z_\tau,q) \longrightarrow \mathbb{Q}_\theta(\mathcal{G}_q\mid\mathcal{G},q)$$

#### Directional Distance Encoding (DDE)
$\mathcal{z}_\tau$ describes some relationship between the triple $\tau=(h,r,t)$ and query $q$, given graph $\mathcal G$. The paper argues that a serious limitation of traditional GNNs is their inability to compute the “distance” between **graph entities** and **query entities**, which is detrimental for multi-hop tasks. The paper uses DDE as $\mathcal{z}_\tau(h,r,t)$. Let $\mathcal{T}_q$ be all entities in $q$ (a question can contain multiple entities). For each entity $e\in\mathcal{T}_q$, first encode it as one-hot vector $\mathbf{s}_e^{(0)}$, then propagate it multiple times and concatenate all results to obtain $\mathbf{s}_e=[\mathbf{s}_e^{(0)}\|\mathbf{s}_e^{(1)}\|\cdots\|\mathbf{s}_e^{(r,1)}\|\cdots]$.

For $z_\tau(\mathcal{G},q)=[\mathbf{s}_h||\mathbf{s}_t]$, this gives:
- $\mathbf{s}_h=[\mathbf{s}_h^{(0)}\|\mathbf{s}_h^{(1)}\|\cdots\|\mathbf{s}_h^{(r,1)}\|\cdots]$
- $\mathbf{s}_t=[\mathbf{s}_t^{(0)}\|\mathbf{s}_t^{(1)}\|\cdots\|\mathbf{s}_h^{(r,1)}\|\cdots]$

Now that we have $\mathcal{z}_\tau(h,r,t)$, computing Eq. 2 still requires $p_\theta(\cdot|z_\tau(\mathcal{G},q),q)$.

> **Question (1):** Eq. 2 shows that we need to calculate $p_\theta(\tau\mid z_\tau,q)$, but the paper explicitly introduces **A Lightweight Implementation For** $p_\theta(\cdot|z_\tau(\mathcal{G},q),q)$. Why are the forms different? Are they the same thing?
>
> One way to understand this (for convenience, let $A:p_\theta(\tau\mid z_\tau,q)$ and $B:p_\theta(\cdot|z_\tau(\mathcal{G},q),q)$) is that both expressions describe the probability that triple $\tau$ appears in the graph, given graph structure $\mathcal G$ and query $q$. In practice, $B$ is an implementation of $A$; it emphasizes how parameters are used during training, while $A$ only indicates **which parameters** affect that probability. So $B$ could even be written as $p_\theta(\cdot|z_\tau(\mathcal{G},q),q,q,\ldots)$.

The paper says the MLP is trained on $[z_q\|z_h\|z_r\|z_t\|z_\tau]$. The query is encoded separately as well (an engineering trick—**redundantly feeding** $q$ helps the model learn the relationship between the query and triple). The encodings come from:
- $z_q,z_h,z_t$: encoded by the embedding model gte-large-en-v1.5.
- $z_r$: looked up in a fixed table.
- $z_\tau$: computed with DDE.

> **Translation note:** This English version was translated with the assistance of an LLM.
