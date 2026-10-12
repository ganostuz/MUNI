---
course: PV021 Neural Networks
topic: Regression / classification outputs and error functions; SE vs CE
slides: 98–102
exam: "Everything in great detail (slide 100 not required)"
tags: [pv021, loss, softmax, cross-entropy, exam]
---

# 05 · Output Activations and Error Functions (slides 98–102)

Up: [[PV021-00 Index]] · Prev: [[PV021-04 MLP and Backpropagation]] · Next: [[PV021-06 Computational Graphs and General Backprop]]

> [!summary] The three standard pairings
> | Task | Output activation | Error function |
> |---|---|---|
> | Regression | identity $y_i=\xi_i$ | mean squared error (MSE) |
> | Multi-class classification | softmax | categorical cross-entropy |
> | Binary classification | logistic sigmoid | binary cross-entropy |

---

## 1. Regression (slide 98)

> [!definition] Regression output and MSE
> - Output activation is typically the **identity**: $y_i = \sigma(\xi_i) = \xi_i$ for $i\in Y$.
> - Training set $\{(\vec x_k,\vec d_k)\mid k=1,\dots,p\}$, $\vec x_k\in\mathbb R^{|X|}$, $\vec d_k\in\mathbb R^{|Y|}$, $\vec d_k = (d_{ki})_{i\in Y}$.
> - **Mean squared error**:
> $$E(\vec w) = \frac1p\sum_{k=1}^pE_k(\vec w),\qquad E_k(\vec w) = \frac12\sum_{i\in Y}\big(y_i(\vec w,\vec x_k) - d_{ki}\big)^2$$
>
> (Compared with slide 86, the factor $\frac1p$ only rescales the gradient.)

## 2. Multi-class classification (slide 99)

> [!definition] Softmax and categorical cross-entropy
> - **Softmax** output activation, with $Y=\{j_1,\dots,j_k\}$:
> $$y_i = \sigma_i(\xi_{j_1},\dots,\xi_{j_k}) = \frac{e^{\xi_i}}{\sum_{j\in Y}e^{\xi_j}}$$
>   (Each output depends on **all** output potentials. The outputs are positive and sum to $1$, so they form a probability distribution over classes.)
> - Training set: $\vec d_k\in\{0,1\}^{|Y|}$ (one-hot), $d_{ki}$ = desired output of neuron $i$ for $\vec x_k$.
> - **(Categorical) cross-entropy**:
> $$E(\vec w) = -\frac1p\sum_{k=1}^p\sum_{i\in Y} d_{ki}\log\big(y_i(\vec w,\vec x_k)\big)$$
>   With one-hot $\vec d_k$ this is $-\frac1p\sum_k \log(\text{predicted probability of the correct class})$, i.e. the negative log-likelihood.

### Gradient with softmax & cross-entropy (slide 100, *not required for the exam*)

Let $V$ be the layer just below the output layer $Y$. Then
$$E(\vec w) = -\frac1p\sum_{k=1}^p\sum_{i\in Y}d_{ki}\Big(\xi_i - \log\sum_{j\in Y}e^{\xi_j}\Big)= -\frac1p\sum_{k=1}^p\sum_{i\in Y}d_{ki}\Big(\sum_{\ell\in V}w_{i\ell}y_\ell - \log\sum_{j\in Y}e^{\sum_{\ell\in V}w_{j\ell}y_\ell}\Big)$$

> [!note]- Result (derived and checked numerically)
> For one example (with $\sum_i d_{ki} = 1$):
> $$\frac{\partial E_k}{\partial\xi_m} = -d_{km} + \Big(\sum_i d_{ki}\Big)\frac{e^{\xi_m}}{\sum_j e^{\xi_j}} = y_m - d_{km}$$
> and therefore $\frac{\partial E_k}{\partial y_\ell} = \sum_{m\in Y}(y_m - d_{km})\,w_{m\ell}$ for $\ell\in V$. The gradient is **"prediction minus target"**, exactly as for identity + MSE.

## 3. Binary classification (slide 101)

> [!definition] Logistic output and binary cross-entropy
> - A single output neuron, $Y=\{o\}$, with the **logistic sigmoid**:
> $$\sigma_o(\xi_o) = \frac{e^{\xi_o}}{e^{\xi_o}+1} = \frac{1}{1+e^{-\xi_o}}$$
> - Training set $\mathcal T = \{(\vec x_1,d_1),\dots,(\vec x_p,d_p)\}$ with $\vec x_k = (x_{k0},x_{k1},\dots,x_{kn})\in\mathbb R^{n+1}$, $x_{k0}=1$, and $d_k\in\{0,1\}$.
> - **(Binary) cross-entropy**:
> $$E(\vec w) = -\sum_{k=1}^p \Big[d_k\log\big(y_o(\vec w,\vec x_k)\big) + (1-d_k)\log\big(1-y_o(\vec w,\vec x_k)\big)\Big]$$
>   Interpretation: $y_o$ is the predicted probability that $d=1$.

---

## 4. Squared error vs logistic output activation (slide 102): why cross-entropy

> [!example] Setting
> Single-neuron model $y=\sigma(w\cdot x) = 1/(1+e^{-w\cdot x})$, $w\in\mathbb R$ (bias ignored). Training set $\mathcal T = \{(x,d)\}$ with $x\in\mathbb R$, $d\in\{0,1\}$. Recall $\sigma' = y(1-y)$.

> [!proof] Squared error $E(w)=\frac12(y-d)^2$
> $$\frac{\partial E}{\partial w} = (y-d)\cdot y\,(1-y)\cdot x$$
> - If $d=1$ and $y\approx0$, then $\frac{\partial E}{\partial w}\approx 0$.
> - If $d=0$ and $y\approx1$, then $\frac{\partial E}{\partial w}\approx 0$.
>
> **The gradient is small even though the model is completely wrong!** The factor $y(1-y)$ kills it: the sigmoid is **saturated**.

> [!proof] Cross-entropy $E(w) = -d\log(y)-(1-d)\log(1-y)$
> For $d=1$:
> $$\frac{\partial E}{\partial w} = -\frac1y\cdot y(1-y)\cdot x = -(1-y)\cdot x$$
> which is close to $-x$ for $y\approx 0$. A large gradient when the model is wrong ✔.
>
> For $d=0$:
> $$\frac{\partial E}{\partial w} = -\frac{1}{1-y}\cdot(-y)(1-y)\cdot x = y\cdot x$$
> which is close to $x$ for $y\approx1$ ✔.
>
> Both cases together: $\frac{\partial E}{\partial w} = (y-d)\,x$. The $\log$ cancels the saturating factor $y(1-y)$.

> [!tip] One-line takeaway
> With a sigmoid or softmax output, use **cross-entropy**. Its gradient w.r.t. the output potential is $y-d$, which is large when the prediction is confidently wrong. Squared error would multiply this by $\sigma'(\xi)$, which vanishes in saturation.

---

## Rehearsal

> [!question]- Which output activation and error for regression / multi-class / binary?
> Identity + MSE; softmax + categorical cross-entropy; logistic sigmoid + binary cross-entropy.

> [!question]- Write softmax and categorical cross-entropy.
> $y_i=e^{\xi_i}/\sum_{j\in Y}e^{\xi_j}$; $E=-\frac1p\sum_k\sum_{i\in Y}d_{ki}\log y_i(\vec w,\vec x_k)$.

> [!question]- Derive $\partial E/\partial w$ for a single logistic neuron with squared error and with cross-entropy.
> SE: $(y-d)y(1-y)x$, which vanishes when the neuron is saturated even if wrong. CE: $-(1-y)x$ for $d=1$ and $yx$ for $d=0$, i.e. $(y-d)x$.

> [!question]- What is $\partial E_k/\partial\xi_m$ for softmax + CE?
> $y_m-d_{km}$ (slide 100, optional).
