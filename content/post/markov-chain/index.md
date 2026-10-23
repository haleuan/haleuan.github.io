+++
categories = ['Deep Learning']
date = '2025-05-08T18:50:00+08:00'
draft = false
showToc = true
tags = ['Machine Learning', 'Algorithms', 'Markov Chains', 'Hidden Markov Models']
title = 'Markov Chains and Hidden Markov Models'
+++

## Introduction

Markov chains are a foundation of machine learning and artificial intelligence, with broad applications in reinforcement learning, natural language processing, finance, weather forecasting, and speech recognition.

> The future is independent of the past given the present  
> The future depends only on the present, not on the past.

This philosophical statement also captures the idea behind a Markov chain: **all information from the past has been captured in the current state, so the future can be predicted from the current state.**

This may sound like an extreme simplification, but it can greatly reduce model complexity. Markov chains are therefore widely used in many time-series models, such as recurrent neural networks (RNNs) and hidden Markov models (HMMs). MCMC also relies on them.

## Stochastic processes

Markov chains are part of the subject of **stochastic processes**, so let us start with a brief introduction.

Simply put, **a stochastic process uses statistical models to predict and handle how something changes over time**. For example, stock prices can be predicted from whether the market rises or falls today, and weather forecasts can predict rain tomorrow or the day after from whether it rains today. These processes can be quantified with mathematical formulas. Given the probabilities of rain or stock prices rising or falling, formulas can derive the state after $N$ days.

The Russian mathematician Andrey Andreyevich Markov studied and proposed a general model that uses mathematics to explain natural changes. It was named the Markov chain. A Markov chain is a stochastic process that transitions between states in a state space and has the property of **memorylessness**: the probability distribution of the next state depends only on the current state and is independent of earlier events in the time sequence. This specific form of memorylessness is called the **Markov property**.

## Mathematical definition

Suppose our sequence of states is $...X_{t−2}, X_{t−1}, X_t, X_{t+1}...$. The conditional probability of the state at time $X_{t+1}$ depends only on the state at the previous time $X_t$:

$$
P(X_{t+1} \mid \ldots X_{t−2}, X_{t−1}, X_t) = P(X_{t+1} \mid X_t)
$$

Since the probability of a state transition at any time **depends only on the preceding state**, the Markov-chain model is determined once we can find the transition probability between any two states in the system.

## Transition probability matrix

Using a Markov chain, we can represent state transitions with a **probability matrix** (also called a **state distribution matrix**), as shown below:

![](/post/markov-chain/file-Markov-Chain-2025120122563360.png)

The figure has two states, $A$ and $B$. The probability of transitioning from $A$ to $A$ is $0.3$, and from $A$ to $B$ is $0.7$. The probability of transitioning from $B$ to $B$ is $0.1$, and from $B$ to $A$ is $0.9$.

- If the initial state is $A$, what is the probability of still being in $A$ after two transitions? It is straightforward:
  $P = A \rightarrow A \rightarrow A + A \rightarrow B \rightarrow A = 0.3 \times 0.3 + 0.7 \times 0.9 = 0.72$
- How can we find the probability of each state after two transitions when the initial and final states are unknown? This is where the **transition probability matrix** comes in. It gives a clear description of all the probabilities.

$$\begin{aligned}
P &= \begin{bmatrix} & A & B \\ A & 0.3 & 0.7 \\ B & 0.9 & 0.1 \end{bmatrix}, \\
& \text{After two transitions:} \\
P &= \begin{bmatrix} & A & B \\ A & 0.3 & 0.7 \\ B & 0.9 & 0.1 \end{bmatrix} \begin{bmatrix} & A & B \\ A & 0.3 & 0.7 \\ B & 0.9 & 0.1 \end{bmatrix} \\
&= \begin{bmatrix} & A & B \\ A & 0.72 & 0.28 \\ B & 0.36 & 0.64 \end{bmatrix}
\end{aligned}$$

With the state matrix, we can easily draw the following conclusions:
- Starting in state $A$, the probability of being in $A$ after $2$ transitions is $0.72$.
- Starting in state $A$, the probability of being in $B$ after $2$ transitions is $0.28$.
- Starting in state $B$, the probability of being in $A$ after $2$ transitions is $0.36$.
- Starting in state $B$, the probability of being in $B$ after $2$ transitions is $0.64$.

### Stability of the state transition matrix

An important property of a state transition matrix is that, after a finite number of transitions, it can reach a **stable probability distribution** that is independent of the initial state distribution. For example:

Suppose the current distribution of the stock market is $[0.3, 0.4, 0.3]$, meaning a $30\%$ probability of a bull market, $40\%$ probability of a bear market, and $30\%$ probability of a flat market. Treat this as the initial state distribution $t_0$ and use the state transition matrix to calculate the states $t_1, t_2, t_3, \dots$. Here is the code:

```python
import numpy as np

matrix = np.matrix([[0.9, 0.075, 0.025],
                    [0.15, 0.8, 0.05],
                    [0.25, 0.25, 0.5]], dtype=float)
vector1 = np.matrix([[0.3, 0.4, 0.3]], dtype=float)

for i in range(100):
    vector1 = vector1 * matrix
    print('Current round: {}'.format(i+1))
    print(vector1)
```

Part of the output is shown below:

```text
Current round: 1
[[ 0.405   0.4175  0.1775]]
...
Current round: 60
[[ 0.625   0.3125  0.0625]]
...
Current round: 100
[[ 0.625   0.3125  0.0625]]
```

From round 60 onward, the state probability distribution remains unchanged at $[0.625, 0.3125, 0.0625]$: a $62.5\%$ probability of a bull market, $31.25\%$ of a bear market, and $6.25\%$ of a flat market.

## An example of a non-Markov process

A process is a Markov process only if it satisfies the Markov-chain property. Consider drawing balls from a bag without replacement:

![](/post/markov-chain/Pasted-image-20241017184056.png)

Clearly, the probability of the current draw depends not only on the color of the ball drawn most recently, but **also on the color of every ball drawn earlier**. Therefore, this process is not a Markov process.

If the balls are returned to the bag after each draw, the process becomes a Markov process.

## Hidden Markov Model (HMM)

### The box-and-ball experiment

Suppose we have three identical boxes, $box$, each containing a number of balls as follows:
1. $box_1: 3\ \text{black},\ 8\ \text{white}$
2. $box_2: 6\ \text{black},\ 4\ \text{white}$
3. $box_3: 4\ \text{black},\ 6\ \text{white}$

The experiment proceeds as follows:
1. Randomly select a box $box_i$.
2. Take one ball from the selected $box_i$.
3. Record its color and return it to the box (classical probability model).

In each trial, we can observe only the color of the ball drawn; we cannot observe the number of the selected $box$.

The relationships in this box-and-ball experiment are shown below:

![](/post/markov-chain/Pasted-image-20241025123340.png)

This figure illustrates two core ideas in a hidden Markov model. The first is **hidden**: the state sequence, or the sequence of boxes, cannot be observed; it is implicit. The second is **Markov**: transitions in the hidden state sequence form a Markov process, with specific transition probabilities between hidden states.

## References

- [A simple and intuitive introduction to Markov chains](https://zhuanlan.zhihu.com/p/448575579)
- [What is a Markov chain?](https://zhuanlan.zhihu.com/p/38764470)
- [MCMC (II): Markov chains](https://www.cnblogs.com/pinard/p/6632399.html)
- [What is a Hidden Markov Model?](https://www.zhihu.com/question/19633865/answer/1635328825)

> **Translation note:** This English version was translated with the assistance of an LLM.
