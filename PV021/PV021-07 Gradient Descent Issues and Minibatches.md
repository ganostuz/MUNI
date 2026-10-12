---
course: PV021 Neural Networks
topic: Practical issues of gradient descent, minibatch size
slides: 112–119
exam: "All details (slide 117: only roughly what the theorem says)"
tags: [pv021, optimization, practical, exam]
---

# 07 · Practical Issues of Gradient Descent (slides 112–119)

Up: [[PV021-00 Index]] · Prev: [[PV021-06 Computational Graphs and General Backprop]] · Next: [[PV021-08 Optimizers and Learning Rate]]

## 1. The list of practical issues (slide 112)

> [!summary] Questions to answer in practice
> **Training efficiency:**
> - What size of minibatch?
> - How to choose the learning rate $\varepsilon(t)$ and control SGD? → [[PV021-08 Optimizers and Learning Rate]]
> - How to pre-process the inputs? → [[PV021-09 Activations, Preprocessing, Initialization]]
> - How to initialize the weights? → [[PV021-09 Activations, Preprocessing, Initialization]]
> - How to choose the desired output values of the network? → [[PV021-05 Output Activations and Error Functions]]
>
> **Quality of the resulting model:**
> - When to stop training? How large a network? Regularization techniques? → [[PV021-10 Normalization and Regularization]]
>
> (The reasoning is illustrated on MLP + MSE; other topologies and error functions have different but related issues.)

---

## 2. Issues in gradient descent (slides 113–116)

> [!note] The error landscape (slide 113)
> - **Small networks:** lots of **local minima** where the descent gets stuck.
> - **Model identifiability problem:** swapping the incoming (and outgoing) weights of neurons $i$ and $j$ gives the same network function → **weight space symmetry**. So minima are never unique: every minimum comes with many equivalent copies.
> - Recent studies: for sufficiently large networks, **all local minima have low values** of the error function.
> - **Saddle points:** one can show (by a combinatorial argument) that larger networks have **exponentially more saddle points than local minima**. At a saddle the gradient is $0$, but the point is a minimum in some directions and a maximum in others.

| Problem | Picture | What happens |
|---|---|---|
| **Too slow descent** (slide 114) | **flat regions** (plateaus) | gradient ≈ 0, so tiny steps and very slow progress |
| **Too fast descent** (slide 115) | **steep cliffs** | the gradient is extremely large, so the descent skips important weight vectors (it jumps far away) |
| **Local vs global structure** (slide 116) | a plateau on the left, a deep valley on the right behind a hill | if we initialize on the left, the local gradient never "sees" the global minimum; GD follows only local information |

---

## 3. Gradient descent in large networks (slide 117): *know roughly*

> [!theorem] Theorem (roughly)
> Assume:
> - "smooth" ReLU activations (**softplus** $\sigma(z) = \log(1+\exp(z))$); in general smooth, non-polynomial, analytic, Lipschitz continuous;
> - inputs $\vec x_k$ of Euclidean norm $1$, desired values $d_k$ with bounded $|d_k|$;
> - the number of hidden neurons per layer is **sufficiently large** (polynomial in certain numerical characteristics of the inputs, roughly measuring their similarity, and **exponential in the depth**);
> - the learning rate is constant and sufficiently small.
>
> Then **gradient descent converges** (with high probability w.r.t. the random initialization) **to a global minimum with zero error at a linear rate**.
> (For **ResNets**, polynomially many neurons per layer w.r.t. depth suffice.)

**Takeaway:** heavy over-parameterization makes the landscape benign, so GD reaches a global minimum.

---

## 4. Issues in computing the gradient (slide 118)

> [!warning] Vanishing and exploding gradients
> $$\frac{\partial E_k}{\partial y_j} = y_j - d_{kj}\ (j\in Y),\qquad \frac{\partial E_k}{\partial y_j} = \sum_{r\in j^\rightarrow}\frac{\partial E_k}{\partial y_r}\cdot\sigma_r'(\xi_r)\cdot w_{rj}\ (j\in Z\setminus(Y\cup X))$$
> Going down $L$ layers multiplies about $L$ factors of the form $\sigma'_r(\xi_r)\,w_{rj}$.
> - If these factors are typically $<1$ (e.g. saturated sigmoids with $\sigma'\le\frac14$, or small weights), the gradient **vanishes** exponentially with depth.
> - If they are typically $>1$ (large weights), it **explodes**.

> [!note] Inexact gradient computation
> - The minibatch gradient is only an **estimate** of the true gradient.
> - The **standard deviation** of the estimate is roughly $\sigma/\sqrt m$, where $m$ is the minibatch size and $\sigma$ is the standard deviation of the gradient estimate for a single training example. (The slide's wording says "variance", but what scales with $\sqrt{m}$ is the standard deviation.)
> - E.g. minibatch size $10\,000$ means **100×** more computation than size $100$, but only **10×** smaller deviation, because $\sqrt{10000/100}=10$.

> [!proof]- Why $\sigma/\sqrt m$
> The minibatch average $\bar g = \frac1m\sum_{k=1}^m g_k$ of i.i.d. per-example gradients with variance $\sigma^2$ has
> $\operatorname{Var}(\bar g) = \frac1{m^2}\sum_{k=1}^m\operatorname{Var}(g_k) = \frac{\sigma^2}{m}$. So the standard deviation is $\sigma/\sqrt m$. (Checked by simulation.)

---

## 5. Minibatch size (slide 119)

> [!summary] Facts about minibatch size
> - **Larger batches** give a more accurate gradient estimate, but with **less than linear returns** (see $\sigma/\sqrt m$).
> - Multicore architectures are usually **underutilized by extremely small batches**.
> - If all examples in a batch are processed in parallel (the typical case), **memory scales with batch size**. On many hardware setups this is the limiting factor.
> - With GPUs, **power-of-2 batch sizes** are common because they give better runtime. Typical: **32 to 256**, with **16** sometimes attempted for large models.
> - **Small batches can have a regularizing effect**, perhaps due to the noise they add to the learning process. In practice, **larger batches can degrade generalization** (Keskar et al., ICLR'17: "generalization gap and sharp minima").

---

## Rehearsal

> [!question]- What is weight space symmetry?
> Swapping hidden neurons $i$ and $j$ (their incoming and outgoing weights) gives the same network function. Minima therefore come in many equivalent copies, and the model is not identifiable.

> [!question]- Local minima vs saddle points in large networks?
> Larger networks have exponentially more saddle points than local minima, and their local minima tend to have low error values.

> [!question]- Name the three landscape problems from slides 114–116.
> Flat regions (too slow), steep cliffs (too fast, skipping important weights), and local vs global structure (local gradient information misleads; it depends on initialization).

> [!question]- Roughly state the theorem on GD in large networks.
> With smooth activations (softplus), normalized inputs, bounded targets, sufficiently wide layers (polynomial in input similarity measures, exponential in depth; polynomial for ResNets) and a small constant learning rate, GD converges with high probability to a zero-error global minimum at a linear rate.

> [!question]- How does gradient noise scale with minibatch size $m$?
> Standard deviation $\approx\sigma/\sqrt m$. 100× more data per batch gives only 10× less deviation.

> [!question]- Why might small batches generalize better?
> Their noise acts as a regularizer. Large batches have been observed to converge to sharp minima that generalize worse.
