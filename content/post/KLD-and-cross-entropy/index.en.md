+++
ShowToc = true
categories = ['MS Research Journey']
date = '2025-05-03T15:59:21+08:00'
draft = false
slug = 'KL散度和交叉熵损失函数'
tags = ['KL Divergence', 'Cross Entropy', 'Deep Learning', 'Machine Learning']
title = 'KL Divergence and Cross Entropy'
+++

## KL Divergence (Kullback-Leibler Divergence, KLD)

Suppose we have two coins:
- A fair coin with the following probability distribution:  
  $$P=\begin{cases}&0.5,&\text{Heads} \\ &0.5, &\text{Tails}\end{cases}$$
- A biased coin with the following probability distribution:  
  $$Q=\begin{cases}&0.9,&\text{Heads} \\ &0.1, &\text{Tails}\end{cases}$$

How can we describe the difference between these two distributions? More importantly, how can we **quantify** this difference?

KL divergence allows us to quantify the difference (or similarity) between two distributions. It measures how one probability distribution $Q(x)$ diverges from a reference distribution $P(x)$, and is defined as:  
$$D_{KL}(Q|| P)=\begin{cases} \sum\limits_i Q(i)\log\frac{Q(i)}{P(i)},\quad \text{discrete} \\ \int Q(i)\log\frac{Q(i)}{P(i)}\ di,\quad \text{continuous}\end{cases}$$

**Note**: KL divergence is **asymmetric**, meaning that $D_{KL}(Q||P) \ne D_{KL}(P||Q)$.
- $D_{KL}(Q||P)$ represents the information loss when using distribution $P$ to approximate $Q$.
- $D_{KL}(P||Q)$ represents the information loss when using distribution $Q$ to approximate $P$.

## Entropy of a Distribution

Entropy is a concept often introduced in high school physics as a measure of disorder in a system. In statistics, **entropy** measures the **uncertainty** of a probability distribution.

For a distribution $P$, its entropy is defined as:  
$$H(P) = -\sum\limits_i P(x_i)\log\big(P(x_i)\big)$$

## From KL Divergence to Cross Entropy

In real-world machine learning tasks, we often use a predicted distribution to approximate the true data distribution. Let $P$ be the **true distribution** and $Q$ be the **predicted distribution**. The **cross entropy** is defined as:  
$$H(P, Q) = -\sum\limits_i P(x_i)\log\big(Q(x_i)\big)$$

We can derive this from KL divergence as follows:  
$$\begin{aligned}
D_{KL}(P||Q) &=  \sum\limits_i P(x_i)\log\left(\frac{P(x_i)}{Q(x_i)}\right) \\
&= \sum\limits_i P(x_i)\left(\log(P_i) - \log(Q_i)\right) \\
&= \sum_i P(x_i)\log(P_{x_i}) - \sum_i P(x_i)\log(Q_{x_i})
\end{aligned}$$

We can observe that $\sum_i P(x_i)\log(P_{x_i}) = -H(P)$, which is the entropy of $P$, and $\sum_i P(x_i)\log(Q_{x_i})$ is the cross entropy $H(P, Q)$. Therefore, we can rewrite the equation as:  
$$D_{KL}(P||Q) = -H(P) + H(P, Q) \quad \Longrightarrow \quad H(P, Q) = D_{KL}(P||Q) + H(P)$$

This shows that  
**Cross Entropy = Entropy of the true distribution + KL Divergence (from true to predicted distribution)**.  
So, when we minimize the cross entropy during training, we are effectively minimizing the KL divergence $D_{KL}(P||Q)$, since the true distribution’s entropy $H(P)$ is constant and independent of the model.

## Using Cross Entropy as the Loss Function in Classification Tasks

In classification problems with one-hot encoded labels, there is exactly one correct class for each prediction. The cross entropy is:  
$$H(P, Q) = -\sum\limits_i P(x_i)\log\big(Q(x_i)\big)$$  
where $P(i)$ is the true probability for class $i$ and $Q(i)$ is the predicted probability for class $i$.

Expanding the sum:  
$$\begin{aligned}
H(P, Q) &= -\sum\limits_i P(x_i)\log(Q(x_i)) \\
&= -\big(0\cdot\log(Q_1) + 0\cdot\log(Q_2) + \ldots + 1\cdot\log(Q_C) + \ldots + 0\cdot\log(Q_N)\big) \\
H(P, Q) &= -\log\big(Q(i = C)\big)
\end{aligned}$$

Here, $C$ is the correct class.

However, note that in practice, we don’t compute the loss for a single sample, but for a **batch** of samples. Therefore, the loss function is typically calculated as the average cross entropy over the batch:  
$$\text{batch\_loss} = -\frac{1}{N}\sum\limits_{i=1}^N\log\big(Q(k_i)\big)$$
