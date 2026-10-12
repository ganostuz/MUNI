---
course: PV021 Neural Networks
topic: Choice of activations, input standardization, weight initialization (LeCun, Glorot, He)
slides: 131–144
exam: "MUST know and understand how normal LeCun init is derived (134–135). Slides 141–142: know one ReLU variant. Slide 143: know that an init↔activation assignment exists"
tags: [pv021, initialization, activation, lecun, exam]
---

# 09 · Activations, Input Preprocessing, Weight Initialization (slides 131–144)

Up: [[PV021-00 Index]] · Prev: [[PV021-08 Optimizers and Learning Rate]] · Next: [[PV021-10 Normalization and Regularization]]

---

## 1. Choice of (hidden) activations (slide 131)

> [!summary] Generic requirements on activation functions
> 1. **Differentiability**, so that gradient descent can be used.
> 2. **Non-linearity**: linear multi-layer networks are equivalent to single-layer ones, since $W_2(W_1\vec x) = (W_2W_1)\vec x$.
> 3. **Monotonicity**: local extrema of the activation function induce local extrema of the error function.
> 4. **"Linearity"**: preserve as much linearity as possible. Linear models are the easiest to fit; find the "minimum" non-linearity needed to solve the task.
>
> The choice of activation is closely tied to **input preprocessing** and the **initial choice of weights**.

## 2. Input preprocessing (slide 132)

> [!definition] Standardization
> - Some inputs may be much larger than others (e.g. a person's height vs weight; a car's max speed in km/h vs its price in CZK).
> - **Large inputs have a greater influence on training** than small ones. Too large inputs may also **slow down learning** by saturating some activation functions.
> - **Typical standardization** of each input component:
>   - average $=0$ (subtract the mean),
>   - variance $=1$ (divide by the standard deviation):
> $$x'_{ki} = \frac{x_{ki}-\mu_i}{s_i},\quad \mu_i=\frac1p\sum_{k=1}^p x_{ki},\quad s_i=\sqrt{\frac1p\sum_{k=1}^p(x_{ki}-\mu_i)^2}$$
> - The mean and standard deviation are estimated from the **training set**.
> - (Normal distribution reminder from the figure: $34.1\%$ in $[0,1\sigma]$, $13.6\%$ in $[1\sigma,2\sigma]$, $2.1\%$ in $[2\sigma,3\sigma]$, $0.1\%$ beyond $3\sigma$, on each side.)

---

## 3. Initial weights: intuition (slide 133)

> [!note] Why initialization matters
> Weights are chosen randomly, but from what distribution? In a deep network:
> - **small weights** make the inner potentials **vanish**;
> - **large weights** make the inner potentials **explode**.
>
> So we want weights for which the **inner potentials are stable**, i.e. similar in all layers of the network.

## 4. Normal LeCun initialization (slide 134) ★ MUST KNOW

> [!definition] Setup and rule
> - Assume the input data have **mean $=0$ and variance $=1$**.
> - Consider a neuron $j$ in the first layer with $n$ inputs. Its weights are drawn from the normal distribution $\mathcal N(0,w^2)$ (variance $w^2$, standard deviation $w$).
> - All random choices are **independent** of each other.
> - **The rule:** choose the standard deviation $w$ of the weights so that the **standard deviation of $\xi_j$** (denote it $o_j$) satisfies $o_j\approx 1$.
> - Basic properties of the variance of independent variables give $o_j = \sqrt n\cdot w$.
> - So putting $w = \sqrt{\frac1n}$ we obtain $o_j = 1$.
> - The same works for higher layers; $n$ is then the number of neurons in the layer one level lower.
>
> This gives **normal LeCun initialization**:
> $$\boxed{w_i\sim\mathcal N\Big(0,\frac1n\Big)}$$

## 5. Derivation of the LeCun initialization (slide 135) ★ MUST KNOW

> [!proof] Proof that $\operatorname{Var}[\xi] = n\cdot w^2$
> Consider a single neuron **without bias**, with inner potential
> $$\xi = \sum_{i=1}^n w_ix_i$$
> Treat all $w_i$ and $x_i$ as **independent** random variables (hence $\xi$ is also a random variable), where
> - $w_i\sim\mathcal N(0,w^2)$ for $i=1,\dots,n$, with $w$ a constant;
> - $\mathbb E x_i = 0$ and $\operatorname{Var}[x_i] = \mathbb E[(x_i-\mathbb E x_i)^2] = 1$ for $i=1,\dots,n$.
>
> **Mean:**
> $$\mathbb E\xi = \mathbb E\sum_{i=1}^n w_ix_i = \sum_{i=1}^n\mathbb E\,w_ix_i \overset{\text{ind.}}{=} \sum_{i=1}^n\mathbb E w_i\,\mathbb E x_i = 0$$
> **Variance of one term:**
> $$\operatorname{Var}[w_ix_i] = \mathbb E[w_i^2x_i^2] - \big(\mathbb E[w_ix_i]\big)^2 \overset{\text{ind.}}{=} \mathbb E[w_i^2]\,\mathbb E[x_i^2] - 0 = w^2$$
> because $\mathbb E[w_i^2] = \operatorname{Var}[w_i] + (\mathbb E w_i)^2 = w^2$ and $\mathbb E[x_i^2] = \operatorname{Var}[x_i] + (\mathbb E x_i)^2 = 1$.
>
> **Variance of the sum.** The terms $w_ix_i$ are independent, so variances add:
> $$\operatorname{Var}[\xi] = \operatorname{Var}\Big[\sum_{i=1}^n w_ix_i\Big] \overset{\text{ind.}}{=} \sum_{i=1}^n\operatorname{Var}[w_ix_i] = \sum_{i=1}^n w^2 = n\cdot w^2$$
> Hence $o_j = \sqrt{\operatorname{Var}[\xi]} = \sqrt n\,w$, and $o_j = 1 \iff w = 1/\sqrt n \iff w_i\sim\mathcal N(0,1/n)$. ∎ (Confirmed by simulation: $\operatorname{Var}[\xi]\approx 1.00$ for $n=100$.)

> [!tip] What to say in the exam
> 1. Goal: keep $\operatorname{Var}(\xi)\approx1$ in every layer, so potentials neither vanish nor explode.
> 2. Assumptions: independence; inputs with mean 0 and variance 1; weights with mean 0 and variance $w^2$.
> 3. The computation above gives $\operatorname{Var}\xi = nw^2$, hence $w^2 = 1/n$ ($n$ = fan-in).
> 4. For higher layers, assume the outputs of the lower layer again have mean ≈ 0 and variance ≈ 1 (true approximately, e.g. for tanh/linear in their near-linear regime).
>
> Note that $\mathcal N(0,\cdot)$ is parameterized by the **variance**: $\mathcal N(0,1/n)$ has standard deviation $1/\sqrt n$.

---

## 6. Normal Glorot (Xavier) initialization (slide 136)

> [!definition] Normal Glorot initialization
> The previous heuristic ignores the **variance of the gradient**: it is only concerned with the "size" of activations in the **forward pass**. Glorot & Bengio (2010) proposed a **normalized initialization**:
> $$\boxed{w_i\sim\mathcal N\Big(0,\frac{2}{m+n}\Big) = \mathcal N\Big(0,\frac{1}{(m+n)/2}\Big)}$$
> - $n$ = number of **inputs** to the layer (fan-in).
> - $m$ = number of **neurons in the layer above** (fan-out).
> - This is a **compromise** between the goal of initializing all layers to have the same **activation variance** and the goal of initializing all layers to have the same **gradient variance**.

> [!note]- Why $m$ appears (the backward-pass analogue of the LeCun argument)
> Backprop computes $\frac{\partial E}{\partial y_j} = \sum_{r\in j^\rightarrow}\frac{\partial E}{\partial y_r}\sigma'_r(\xi_r)w_{rj}$, a sum of $m$ terms. The same variance computation (with $\sigma'\approx 1$ near 0 and independence) gives $\operatorname{Var}\approx m\,w^2\operatorname{Var}\big(\tfrac{\partial E}{\partial y_r}\big)$, so gradient variance is preserved iff $w^2 = 1/m$. Forward needs $w^2=1/n$. Glorot takes the variance $1/\frac{m+n}{2}$, using the average of fan-in and fan-out.

## 7. Uniform LeCun initialization (slide 137)

> [!definition] Uniform LeCun
> Same assumptions, but weights from the uniform distribution $U(-w,w)$. We again want the standard deviation $o_j$ of $\xi_j$ to be $\approx1$. Basic properties of the variance give
> $$o_j = \sqrt{\frac n3}\cdot w$$
> so putting $w = \sqrt{\frac3n}$ gives $o_j = 1$:
> $$\boxed{w_i\sim U\Big(-\sqrt{\tfrac3n},\ \sqrt{\tfrac3n}\Big)}$$

> [!proof]- Why $\sqrt{n/3}$
> For $W\sim U(-w,w)$: $\mathbb EW=0$ and $\operatorname{Var}W = \mathbb E W^2 = \frac{1}{2w}\int_{-w}^w t^2\,dt = \frac{w^2}{3}$. Then, as in the LeCun derivation, $\operatorname{Var}\xi = n\cdot\frac{w^2}{3}$, so $o_j = \sqrt{n/3}\,w$. ∎

## 8. Uniform Glorot initialization (slide 138)

> [!definition] Uniform Glorot (Xavier)
> Normalize w.r.t. both the forward and backward passes ($n$ = inputs to the layer, $m$ = neurons in the layer above):
> $$\boxed{w_i\sim U\Big(-\sqrt{\tfrac{6}{m+n}},\ \sqrt{\tfrac{6}{m+n}}\Big) = U\Big(-\sqrt{\tfrac{3}{(m+n)/2}},\ \sqrt{\tfrac{3}{(m+n)/2}}\Big)}$$
> Check: the variance of $U(-a,a)$ is $a^2/3 = \frac{6}{3(m+n)} = \frac{2}{m+n}$, the same variance as normal Glorot ✔.

---

## 9. Modern activation functions (slides 139, 141–142)

> [!definition] ReLU (slide 139)
> $$\sigma(\xi) = \max\{0,\xi\}$$
> - **THE default** activation function recommended for most feed-forward neural networks.
> - As close to a linear function as possible; very simple; **does not saturate** for large potentials.
> - **Dead for negative potentials**: zero gradient there.

> [!definition] Other ReLU variants (slide 141): know at least one
> - **Leaky ReLU**, e.g. $\sigma(\xi)=\max(\alpha\xi,\xi)$ with a small $\alpha$ such as $0.1$ (slide 34): generalizes ReLU and is **not dead for negative potentials**. Experimentally not much better than ReLU.
> - **ELU** ("smoothed" ReLU):
> $$\sigma(\xi) = \begin{cases}\alpha(\exp(\xi)-1) & \xi<0\\ \xi & \xi\ge0\end{cases}$$
>   $\alpha$ is a parameter; ELU converges to $-\alpha$ as $\xi\to-\infty$. Unlike ReLU it is smooth and always has a non-zero gradient (but it saturates), and it is slower to compute.
> - **SELU** (scaled ELU):
> $$\sigma(\xi) = \lambda\begin{cases}\alpha(\exp(\xi)-1) & \xi<0\\ \xi & \xi\ge0\end{cases}$$
>   **Self-normalizing**: the output of each layer tends to preserve mean ≈ 0 and standard deviation ≈ 1 for $\lambda\approx1.050$, $\alpha\approx1.673$, properly initialized weights (LeCun) and normalized inputs (mean 0, std 1).

## 10. Normal He initialization (slide 140)

> [!definition] He initialization
> - ReLU is not as sensitive to a large variance of the inner potential as sigmoidal functions are.
> - Still, it is good for the variance to be constant (at least because of the output layer).
> - **LeCun initialization cannot be justified for ReLU**: ReLU is not symmetric, so even if $\xi_j$ has variance $1$, this is not true of the output; **the variance is halved**.
>
> Modifying normal LeCun to account for the halving gives **normal He initialization**:
> $$\boxed{w_i\sim\mathcal N\Big(0,\frac{2}{n}\Big)}\qquad\Big(\text{LeCun is } w_i\sim\mathcal N\big(0,\tfrac1n\big)\Big)$$

> [!proof]- Where the factor 2 comes from (checked by simulation)
> Let $\xi$ be symmetric around 0 with $\operatorname{Var}\xi=1$, and $y=\mathrm{ReLU}(\xi)$. By symmetry, $\mathbb E[y^2] = \mathbb E[\xi^2\mathbf 1_{\xi>0}] = \tfrac12\mathbb E[\xi^2] = \tfrac12$. (Precisely, it is the **second moment** $\mathbb E[y^2]$ that halves; that is the quantity entering the next layer.)
> In the next layer, $\xi' = \sum_{i=1}^n w_iy_i$ with $\mathbb Ew_i = 0$, so $\operatorname{Var}\xi' = \sum_i\mathbb E[w_i^2]\mathbb E[y_i^2] = n\,w^2\cdot\tfrac12$. Setting this to $1$ gives $w^2 = 2/n$. ∎

## 11. Initialization ↔ activation assignment (slide 143): know that it *exists*

$n$ = number of inputs to the initialized layer, $m$ = number of neurons in the layer.

| Initialization | Distribution | Suitable for |
|---|---|---|
| normal Glorot | $\mathcal N(0,\frac{2}{m+n})$ | none (linear), tanh, logistic, softmax |
| normal He | $\mathcal N(0,\frac2n)$ | ReLU, leaky ReLU |
| normal LeCun | $\mathcal N(0,\frac1n)$ | SELU (by the authors) |

## 12. How to choose the hidden activation (slide 144)

- **The default is ReLU.**
- According to Aurélien Géron: **SELU > ELU > leakyReLU > ReLU > tanh > logistic**.

---

## Rehearsal

> [!question]- State the four generic requirements on activation functions.
> Differentiability (for GD), non-linearity (otherwise layers collapse into one linear map), monotonicity (otherwise extra local extrema of $E$), and "linearity" (keep as much linearity as possible).

> [!question]- Derive normal LeCun initialization. ★
> $\xi=\sum_{i=1}^nw_ix_i$ with independent $w_i\sim\mathcal N(0,w^2)$ and $x_i$ of mean 0, variance 1. $\mathbb E\xi=\sum\mathbb Ew_i\mathbb Ex_i=0$. $\operatorname{Var}[w_ix_i]=\mathbb E[w_i^2]\mathbb E[x_i^2]-0=w^2$. $\operatorname{Var}\xi=\sum\operatorname{Var}[w_ix_i]=nw^2$. Setting the std $o_j=\sqrt n\,w=1$ gives $w_i\sim\mathcal N(0,1/n)$.

> [!question]- Uniform LeCun bound and why?
> $U(-\sqrt{3/n},\sqrt{3/n})$, because $\operatorname{Var}U(-w,w)=w^2/3$, so $o_j=\sqrt{n/3}\,w$.

> [!question]- Normal / uniform Glorot? What are $n$ and $m$?
> $\mathcal N(0,\frac2{m+n})$ and $U(\pm\sqrt{6/(m+n)})$. $n$ = inputs to the layer (fan-in), $m$ = neurons in the layer above (fan-out). A compromise between forward-activation variance and backward-gradient variance.

> [!question]- Why He, and what is it?
> ReLU halves the (second moment of the) variance because it is not symmetric, so double the LeCun variance: $\mathcal N(0,2/n)$.

> [!question]- Define one ReLU variant.
> ELU: $\alpha(e^\xi-1)$ for $\xi<0$, $\xi$ for $\xi\ge0$; it tends to $-\alpha$ and has a non-zero gradient everywhere. (Or leaky ReLU $\max(0.1\xi,\xi)$; or SELU = $\lambda\cdot$ELU with $\lambda\approx1.050$, $\alpha\approx1.673$, self-normalizing.)

> [!question]- Why standardize inputs?
> Large inputs dominate training and can saturate activations, slowing learning. Standardizing to mean 0 and variance 1 (statistics from the training set) also matches the assumptions behind LeCun/Glorot/He initialization.
