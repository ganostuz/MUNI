---
course: PV021 Neural Networks
topic: RNN architecture, example, formal model, binary adder, training, BPTT (derivation), long-term dependencies
slides: 237–248 (LSTM omitted this year)
exam: "All details incl. the handwritten stuff. May be asked to DERIVE backprop for RNN"
tags: [pv021, rnn, bptt, proofs, exam]
---

# 13 · Recurrent Neural Networks (slides 237–248)

Up: [[PV021-00 Index]] · Prev: [[PV021-12 Explainable AI]] · Next: [[PV021-14 Attention and Transformers]]

---

## 1. Architecture (slide 237)

```
        OUTPUT      y₁ … y_N          (red)
                   ↗  ↑  ↖
        HIDDEN  ⟲ h₁ ⇄ h₂ ⇄ … ⇄ h_H ⟲  (blue: self-loops + all-to-all recurrent edges)
                   ↖  ↑  ↗
        INPUT       x₁ … x_M          (green)
```
- Input $\vec x = (x_1,\dots,x_M)$
- Hidden $\vec h = (h_1,\dots,h_H)$, with recurrent connections from hidden to hidden (including self-loops)
- Output $\vec y = (y_1,\dots,y_N)$

## 2. RNN example (slide 238, handwritten)

Activation: unit step, $\sigma(\xi)=1$ if $\xi\ge0$, else $0$. No biases. $M=2$ inputs, $H=2$ hidden, $N=1$ output.

Weights as read from the handwritten figure; they reproduce the slide's sequence exactly (checked):

| | from $x_1$ | from $x_2$ | from $h_1(t-1)$ | from $h_2(t-1)$ |
|---|---|---|---|---|
| to $h_1$ | $U_{11}=1$ | $U_{12}=-1$ | $W_{11}=-1$ (self-loop) | $W_{12}=0$ |
| to $h_2$ | $U_{21}=-1$ | $U_{22}=1$ | $W_{21}=0$ | $W_{22}=-2$ (self-loop) |

Output: $y = \sigma(-1\cdot h_1 + 1\cdot h_2)$, i.e. $V = (-1,\ 1)$.

| $t$ | $\vec x_t$ | $\vec h_{t-1}$ | $\xi^h_1 = x_1 - x_2 - h_1$ | $\xi^h_2 = -x_1 + x_2 - 2h_2$ | $\vec h_t$ | $y_t=\sigma(-h_1+h_2)$ |
|---|---|---|---|---|---|---|
| 1 | $(0,0)$ | $(0,0)$ | $0$ | $0$ | $(1,1)$ | $\sigma(0)=1$ |
| 2 | $(1,0)$ | $(1,1)$ | $1-0-1=0$ | $-1+0-2=-3$ | $(1,0)$ | $\sigma(-1)=0$ |
| 3 | $(1,1)$ | $(1,0)$ | $1-1-1=-1$ | $-1+1-0=0$ | $(0,1)$ | $\sigma(1)=1$ |

Result: $\vec y_1=1$, $\vec y_2=0$, $\vec y_3=1$; $\vec h_0=(0,0)$, $\vec h_1=(1,1)$, $\vec h_2=(1,0)$, $\vec h_3=(0,1)$. ✔

> [!tip] Unrolling (third version of slide 238)
> Draw one copy of the network per time step. The recurrent edges become forward edges from the hidden layer at time $t$ to the hidden layer at time $t+1$, with **the same weights at every step**. An unrolled RNN is a deep feed-forward network with shared weights, which is why backprop works on it (BPTT).

## 3. RNN formally (slides 239–240)

> [!definition] Formal RNN
> - $M$ inputs $\vec x=(x_1,\dots,x_M)$, $H$ hidden neurons $\vec h = (h_1,\dots,h_H)$, $N$ output neurons $\vec y=(y_1,\dots,y_N)$.
> - Weights:
>   - $U_{kk'}$ from input $x_{k'}$ to hidden $h_k$
>   - $W_{kk'}$ from hidden $h_{k'}$ to hidden $h_k$
>   - $V_{kk'}$ from hidden $h_{k'}$ to output $y_k$
> - **Input sequence** $\mathbf x = \vec x_1,\dots,\vec x_T$, $\vec x_t = (x_{t1},\dots,x_{tM})$.
> - **Hidden sequence** $\mathbf h = \vec h_0,\vec h_1,\dots,\vec h_T$, $\vec h_t = (h_{t1},\dots,h_{tH})$, with $\vec h_0 = (0,\dots,0)$ and
> $$h_{tk} = \sigma\Bigg(\sum_{k'=1}^MU_{kk'}x_{tk'} + \sum_{k'=1}^HW_{kk'}h_{(t-1)k'}\Bigg)$$
> - **Output sequence** $\mathbf y = \vec y_1,\dots,\vec y_T$, $\vec y_t = (y_{t1},\dots,y_{tN})$, where
> $$y_{tk} = \sigma\Bigg(\sum_{k'=1}^HV_{kk'}h_{tk'}\Bigg)$$

## 4. Matrix form (slide 241)

$$\vec h_0 = (0,\dots,0),\qquad \vec h_t = \sigma(U\vec x_t + W\vec h_{t-1}),\qquad \vec y_t = \sigma(V\vec h_t)$$
$U\in\mathbb R^{H\times M}$, $W\in\mathbb R^{H\times H}$, $V\in\mathbb R^{N\times H}$; $\sigma$ is applied component-wise.

## 5. Comments (slide 242)

- $\vec h_t$ is the **memory** of the network: it captures what happened in all previous steps (with decaying quality).
- The RNN **shares weights $U,V,W$ along the sequence**. This is similar to CNNs: there weights are shared **spatially** over images, here **temporally** over sequences.
- RNNs can deal with **sequences of variable length** (an MLP accepts only fixed-dimension vectors).

---

## 6. Binary adder (slide 243)

> [!example] Task
> Design a recurrent network with a **single hidden layer** that works as a **binary adder**.
> Input: two binary numbers, e.g. $111$ and $101$, with the **least significant bit on the left**. The input sequence is $(1,1),(1,0),(1,1)$. The output should be $0,0,1$ (the final carry is ignored).
> Check: $7+5=12=1100_2$; the LSB-first digits are $0,0,1,(1)$ ✔.

> [!example]- One possible solution (my construction, verified for all pairs of 6-bit numbers)
> Use step activations **with biases** (or, equivalently, a constant input $1$). Biases are needed: without them $\vec h=\vec 0,\ \vec x = \vec 0$ gives $\sigma(0)=1$, so an all-zero state could never output 0.
> Let $c_{t}$ be the carry into step $t$; it is stored in the hidden state. Three hidden neurons count how many of $a_t, b_t, c_t$ are 1:
> $$h_{t1} = \sigma(a_t + b_t + h_{(t-1)2} - 1),\quad h_{t2} = \sigma(a_t+b_t+h_{(t-1)2} - 2),\quad h_{t3} = \sigma(a_t+b_t+h_{(t-1)2}-3)$$
> i.e. $h_{t1}=[\text{sum}\ge1]$, $h_{t2}=[\text{sum}\ge2]$ (= the new **carry**), $h_{t3}=[\text{sum}\ge3]$.
> Output (sum bit = 1 iff the count is 1 or 3):
> $$y_t = \sigma(h_{t1} - h_{t2} + h_{t3} - 1)$$
> Matrices: $U = \begin{pmatrix}1&1\\1&1\\1&1\end{pmatrix}$, $W = \begin{pmatrix}0&1&0\\0&1&0\\0&1&0\end{pmatrix}$, hidden biases $(-1,-2,-3)$, $V=(1,-1,1)$, output bias $-1$.
> Trace for $111+101$: $t=1$: count $2$, $\vec h=(1,1,0)$, $y=0$, carry 1. $t=2$: count $1+0+1=2$, $y=0$, carry 1. $t=3$: count $1+1+1=3$, $\vec h=(1,1,1)$, $y=1$. Output $0,0,1$ ✔.

---

## 7. Training (slides 244–246)

> [!definition] Training set (slide 244)
> $$\mathcal T = \big\{(\mathbf x_1,\mathbf d_1),\dots,(\mathbf x_p,\mathbf d_p)\big\}$$
> - each $\mathbf x_\ell = \vec x_{\ell1},\dots,\vec x_{\ell T_\ell}$ is an input sequence,
> - each $\mathbf d_\ell = \vec d_{\ell1},\dots,\vec d_{\ell T_\ell}$ is the expected output sequence,
> - $\vec x_{\ell t} = (x_{\ell t1},\dots,x_{\ell tM})$ is an input vector and $\vec d_{\ell t} = (d_{\ell t1},\dots,d_{\ell tN})$ an expected output vector. (Lengths $T_\ell$ may differ.)

> [!definition] Error function (slide 245)
> Consider a training set with a **single element** $(\mathbf x,\mathbf d)$ (drop the index $\ell$): $\mathbf x = \vec x_1,\dots,\vec x_T$, $\mathbf d = \vec d_1,\dots,\vec d_T$. The squared error is
> $$E_{(\mathbf x,\mathbf d)} = \sum_{t=1}^T\sum_{k=1}^N\frac12(y_{tk} - d_{tk})^2$$
> where $\mathbf y = \vec y_1,\dots,\vec y_T$ are the network outputs and $y_{tk}$ is the $k$-th component of $\vec y_t$.

> [!algorithm] Gradient descent, single training example (slide 246)
> - Initialize all weights randomly close to 0.
> - In step $\ell+1$ ($\ell = 0,1,2,\dots$) compute new weights from the old ones:
> $$U^{(\ell+1)}_{kk'} = U^{(\ell)}_{kk'} - \varepsilon(\ell)\cdot\frac{\partial E_{(\mathbf x,\mathbf d)}}{\partial U_{kk'}},\quad V^{(\ell+1)}_{kk'} = V^{(\ell)}_{kk'} - \varepsilon(\ell)\cdot\frac{\partial E_{(\mathbf x,\mathbf d)}}{\partial V_{kk'}},\quad W^{(\ell+1)}_{kk'} = W^{(\ell)}_{kk'} - \varepsilon(\ell)\cdot\frac{\partial E_{(\mathbf x,\mathbf d)}}{\partial W_{kk'}}$$
> **This is THE learning algorithm that modifies the weights!** Backpropagation only computes the derivatives.

---

## 8. Backpropagation through time (slide 247)

**Backpropagation computes the derivatives of $E$; no weights are modified.**

Notation for the derivation: the potentials are
$$\xi^h_{tk} = \sum_{k'}U_{kk'}x_{tk'} + \sum_{k'}W_{kk'}h_{(t-1)k'},\qquad \xi^y_{tk} = \sum_{k'}V_{kk'}h_{tk'}$$
so $h_{tk}=\sigma(\xi^h_{tk})$ and $y_{tk}=\sigma(\xi^y_{tk})$. On the slide, $\sigma'$ is shorthand for $\sigma'$ evaluated at the potential of the neuron in question.

> [!important] BPTT formulas (slide 247; $\sigma'$ arguments written explicitly)
> **Weights:**
> $$\frac{\partial E}{\partial U_{kk'}} = \sum_{t=1}^T\frac{\partial E}{\partial h_{tk}}\cdot\sigma'(\xi^h_{tk})\cdot x_{tk'}\qquad k'=1,\dots,M$$
> $$\frac{\partial E}{\partial V_{kk'}} = \sum_{t=1}^T\frac{\partial E}{\partial y_{tk}}\cdot\sigma'(\xi^y_{tk})\cdot h_{tk'}\qquad k'=1,\dots,H$$
> $$\frac{\partial E}{\partial W_{kk'}} = \sum_{t=1}^T\frac{\partial E}{\partial h_{tk}}\cdot\sigma'(\xi^h_{tk})\cdot h_{(t-1)k'}\qquad k'=1,\dots,H$$
> **Backpropagation:**
> $$\frac{\partial E}{\partial y_{tk}} = y_{tk} - d_{tk}\qquad\text{(assuming squared error)}$$
> $$\frac{\partial E}{\partial h_{tk}} = \sum_{k'=1}^N\frac{\partial E}{\partial y_{tk'}}\cdot\sigma'(\xi^y_{tk'})\cdot V_{k'k} + \sum_{k'=1}^H\frac{\partial E}{\partial h_{(t+1)k'}}\cdot\sigma'(\xi^h_{(t+1)k'})\cdot W_{k'k}$$
> with $\frac{\partial E}{\partial h_{(T+1)k'}} := 0$. Compute for $t = T, T-1,\dots,1$, backwards in time.

### ★ Derivation (be able to reproduce)

> [!proof] Step 0: what "$\partial E/\partial h_{tk}$" means
> Unroll the network into a DAG with nodes $x_{tk}$, $h_{tk}$, $y_{tk}$ ($t=1..T$). $\frac{\partial E}{\partial h_{tk}}$ is the derivative of $E$ w.r.t. the value $h_{tk}$, taking into account **all** later computations that depend on it, exactly like $\frac{\partial E_k}{\partial y_j}$ for an MLP. The weights $U,V,W$ are **shared across time**: each appears once per time step. By the same untie-and-sum argument as for CNNs ([[PV021-11 Convolutional Networks]]), the derivative w.r.t. a shared weight is the **sum over $t$** of the derivatives w.r.t. its per-step copies.

> [!proof] Step 1: weight derivatives
> At time $t$, the copy of $U_{kk'}$ appears only in $\xi^h_{tk}$, with $\frac{\partial\xi^h_{tk}}{\partial U_{kk'}} = x_{tk'}$, and $\xi^h_{tk}$ affects $E$ only through $h_{tk}=\sigma(\xi^h_{tk})$. So
> $$\frac{\partial E}{\partial U_{kk'}} = \sum_{t=1}^T\frac{\partial E}{\partial h_{tk}}\cdot\sigma'(\xi^h_{tk})\cdot x_{tk'}.$$
> Likewise the copy of $W_{kk'}$ at time $t$ is in $\xi^h_{tk}$ with $\frac{\partial\xi^h_{tk}}{\partial W_{kk'}} = h_{(t-1)k'}$, and the copy of $V_{kk'}$ is in $\xi^y_{tk}$ with $\frac{\partial\xi^y_{tk}}{\partial V_{kk'}} = h_{tk'}$, giving the other two formulas.

> [!proof] Step 2: derivatives w.r.t. outputs and hidden states
> **Outputs:** $\frac{\partial E}{\partial y_{tk}} = \frac{\partial}{\partial y_{tk}}\sum_{t',k''}\frac12(y_{t'k''}-d_{t'k''})^2 = y_{tk}-d_{tk}$.
> **Hidden:** $h_{tk}$ directly influences exactly
> - the outputs at the **same time**, $y_{tk'}$ for $k'=1..N$, via $\xi^y_{tk'}$, with $\frac{\partial\xi^y_{tk'}}{\partial h_{tk}} = V_{k'k}$;
> - the hidden neurons at the **next time step**, $h_{(t+1)k'}$ for $k'=1..H$, via $\xi^h_{(t+1)k'}$, with $\frac{\partial\xi^h_{(t+1)k'}}{\partial h_{tk}} = W_{k'k}$.
>
> These are its successors in the unrolled DAG. By the chain rule (sum over successors):
> $$\frac{\partial E}{\partial h_{tk}} = \sum_{k'=1}^N\frac{\partial E}{\partial y_{tk'}}\sigma'(\xi^y_{tk'})V_{k'k} + \sum_{k'=1}^H\frac{\partial E}{\partial h_{(t+1)k'}}\sigma'(\xi^h_{(t+1)k'})W_{k'k}.$$
> For $t=T$ there is no next step, so the second sum is $0$. The recursion goes **backwards in time**, which gives the name **backpropagation through time (BPTT)**. ∎
> (All three weight gradients were checked against finite differences.)

> [!example] Matrix form (handy for writing quickly)
> Let $\boldsymbol\delta^y_t = \sigma'(\xi^y_t)\circ(\vec y_t-\vec d_t)$ and $\boldsymbol\delta^h_t = \sigma'(\xi^h_t)\circ\frac{\partial E}{\partial\vec h_t}$. Then
> $$\frac{\partial E}{\partial\vec h_t} = V^\top\boldsymbol\delta^y_t + W^\top\boldsymbol\delta^h_{t+1}\ (\boldsymbol\delta^h_{T+1}=0),\qquad \frac{\partial E}{\partial U} = \sum_t\boldsymbol\delta^h_t\,\vec x_t^\top,\quad \frac{\partial E}{\partial W} = \sum_t\boldsymbol\delta^h_t\,\vec h_{t-1}^\top,\quad \frac{\partial E}{\partial V} = \sum_t\boldsymbol\delta^y_t\,\vec h_t^\top$$

---

## 9. Long-term dependencies (slide 248)

$$\frac{\partial E}{\partial h_{tk}} = \sum_{k'=1}^N\frac{\partial E}{\partial y_{tk'}}\cdot\sigma'\cdot V_{k'k} + \sum_{k'=1}^H\frac{\partial E}{\partial h_{(t+1)k'}}\cdot\sigma'\cdot W_{k'k}$$

> [!warning] Vanishing / exploding gradients through time
> - Unless $\sum_{k'=1}^H\sigma'\cdot W_{k'k}\approx1$, the gradient either **vanishes** or **explodes**.
> - For a large $T$ (a **long-term dependency**), the gradient "deeper" in the past tends to be too small (or too large).
> - Why: unfolding the recursion, the contribution of the error at time $t+\tau$ to $\frac{\partial E}{\partial \vec h_t}$ contains the product of $\tau$ Jacobians $\prod\big(\operatorname{diag}(\sigma'(\xi^h_{s}))\,W\big)^\top$, roughly a factor $\lambda^\tau$. It goes to 0 if $\lambda<1$ and blows up if $\lambda>1$.
> - **A solution: LSTM etc.** (LSTM is currently a bit obsolete. The main idea is to decompose $W$ into several matrices, each responsible for a different task: one for memory, one for the output at each step, etc.)
> - *LSTM (slides 249–252) was omitted this year.*

---

## Rehearsal

> [!question]- Write the RNN equations (component and matrix form).
> $h_{tk}=\sigma(\sum_{k'}U_{kk'}x_{tk'}+\sum_{k'}W_{kk'}h_{(t-1)k'})$, $y_{tk}=\sigma(\sum_{k'}V_{kk'}h_{tk'})$, $\vec h_0=\vec 0$. Matrix form: $\vec h_t=\sigma(U\vec x_t+W\vec h_{t-1})$, $\vec y_t=\sigma(V\vec h_t)$.

> [!question]- What are $U$, $W$, $V$?
> $U_{kk'}$: input $x_{k'}$ → hidden $h_k$. $W_{kk'}$: hidden $h_{k'}$ → hidden $h_k$ (recurrent). $V_{kk'}$: hidden $h_{k'}$ → output $y_k$.

> [!question]- Compute the slide-238 example.
> With $U=\begin{pmatrix}1&-1\\-1&1\end{pmatrix}$, $W=\operatorname{diag}(-1,-2)$, $V=(-1,1)$, step activation: $\vec h_1=(1,1),y_1=1$; $\vec h_2=(1,0),y_2=0$; $\vec h_3=(0,1),y_3=1$.

> [!question]- Write all BPTT formulas. ★
> $\frac{\partial E}{\partial U_{kk'}}=\sum_t\frac{\partial E}{\partial h_{tk}}\sigma'(\xi^h_{tk})x_{tk'}$; $\frac{\partial E}{\partial V_{kk'}}=\sum_t\frac{\partial E}{\partial y_{tk}}\sigma'(\xi^y_{tk})h_{tk'}$; $\frac{\partial E}{\partial W_{kk'}}=\sum_t\frac{\partial E}{\partial h_{tk}}\sigma'(\xi^h_{tk})h_{(t-1)k'}$; $\frac{\partial E}{\partial y_{tk}}=y_{tk}-d_{tk}$; $\frac{\partial E}{\partial h_{tk}}=\sum_{k'}\frac{\partial E}{\partial y_{tk'}}\sigma'V_{k'k}+\sum_{k'}\frac{\partial E}{\partial h_{(t+1)k'}}\sigma'W_{k'k}$, with $\frac{\partial E}{\partial h_{T+1}} = 0$.

> [!question]- Why are the weight gradients sums over $t$?
> $U,V,W$ are shared across time steps; the derivative w.r.t. a shared weight is the sum of the derivatives w.r.t. its per-time-step copies (untie and sum).

> [!question]- Why do RNNs struggle with long-term dependencies?
> The gradient from step $t+\tau$ back to step $t$ multiplies $\tau$ factors $\sigma'W$. Unless $\sum_{k'}\sigma'W_{k'k}\approx 1$, it vanishes or explodes exponentially in $\tau$. LSTM-like gating is the fix.

> [!question]- Binary adder: input/output for 111 + 101 (LSB left)?
> Input $(1,1),(1,0),(1,1)$; output $0,0,1$ (final carry ignored).
