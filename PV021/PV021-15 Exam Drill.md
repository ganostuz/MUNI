---
course: PV021 Neural Networks
topic: Final rehearsal – everything you must be able to produce on paper
tags: [pv021, rehearsal, exam]
---

# 15 · Exam Drill: Must-Produce-on-Paper Checklist

Up: [[PV021-00 Index]]

> [!warning] Rule of the exam
> Every part listed in the exam manual is **mandatory even for grade E**. Missing a single part means **F**. Tick each box only once you can write it **from memory, without looking**.

## A. Proofs and derivations (write out fully)

- [ ] Geometric picture of a neuron: hyperplane, normal $\vec w$, $D=-w_0/\|\vec w\|$, point distance $\frac{|w_0+\sum w_ix_i'|}{\|\vec w\|}$ → [[PV021-01 Formal Neuron and Geometry]]
- [ ] XOR is not linearly separable (4 inequalities) → [[PV021-01 Formal Neuron and Geometry]]
- [ ] **Two-layer step MLPs compute all Boolean functions** ($N_{\vec v}$, potential $=-d_H$, OR) → [[PV021-03 Expressive Power and Computability]]
- [ ] Idea of ReLU universality: the hat $\mathrm{ReLU}(sx+\ell)-\mathrm{ReLU}(2sx)+\mathrm{ReLU}(sx-\ell)$, sums of hats → [[PV021-03 Expressive Power and Computability]]
- [ ] **MLP gradient formula + derivation** (chain rule, both parts) → [[PV021-04 MLP and Backpropagation]]
- [ ] Gradient algorithm (forward, backward, accumulate) + complexity proof sketch → [[PV021-04 MLP and Backpropagation]]
- [ ] SE vs CE for a logistic neuron: both derivatives and the conclusion → [[PV021-05 Output Activations and Error Functions]]
- [ ] **General backprop in computational graphs** (slide 109) + MLP Jacobian $\dot\sigma^nW^n$ → [[PV021-06 Computational Graphs and General Backprop]]
- [ ] Minibatch gradient std $\sigma/\sqrt m$ → [[PV021-07 Gradient Descent Issues and Minibatches]]
- [ ] **Normal LeCun derivation** $\operatorname{Var}\xi = nw^2$ ★ → [[PV021-09 Activations, Preprocessing, Initialization]]
- [ ] Uniform LeCun ($\sqrt{3/n}$), Glorot normal/uniform, He ($2/n$) and their reasons → [[PV021-09 Activations, Preprocessing, Initialization]]
- [ ] Weight decay ⇔ L2 regularization → [[PV021-10 Normalization and Regularization]]
- [ ] **CNN backprop derivation** (shared weights $[ji]$, dense/conv successors, max-pooling) → [[PV021-11 Convolutional Networks]]
- [ ] **RNN BPTT derivation** (all five formulas) → [[PV021-13 Recurrent Networks]]
- [ ] **Self-attention backprop** (at least with $\alpha_{ij}=e_{ij}$) → [[PV021-14 Attention and Transformers]]
- [ ] Positional encoding linear-shift property (rotation) → [[PV021-14 Attention and Transformers]]

## B. Formulas to write instantly

| # | Formula | Where |
|---|---|---|
| 1 | $\xi=w_0+\sum_{i=1}^nw_ix_i$, $y=\sigma(\xi)$; step / logistic $\frac1{1+e^{-\lambda\xi}}$ / tanh / ReLU | 01, 02 |
| 2 | $\frac{\partial E_k}{\partial w_{ji}}=\frac{\partial E_k}{\partial y_j}\sigma'_j(\xi_j)y_i$; $\frac{\partial E_k}{\partial y_j}=y_j-d_{kj}$ or $\sum_{r\in j^\to}\frac{\partial E_k}{\partial y_r}\sigma'_r(\xi_r)w_{rj}$ | 04 |
| 3 | GD $\Delta w=-\varepsilon\,\partial E/\partial w$; SGD $\Delta\vec w=-\varepsilon\sum_{k\in T}\nabla E_k$ | 04 |
| 4 | MSE $\frac1p\sum_k\frac12\sum_i(y_i-d_{ki})^2$; softmax $\frac{e^{\xi_i}}{\sum_je^{\xi_j}}$; CE $-\frac1p\sum_k\sum_id_{ki}\log y_i$; BCE $-\sum_k[d_k\log y+(1-d_k)\log(1-y)]$ | 05 |
| 5 | $\frac{\partial\mathbf y^o}{\partial\mathbf y^n}=\sum_{m\in n^\to}\frac{\partial\mathbf y^o}{\partial\mathbf y^m}\frac{\partial\mathbf y^m}{\partial\mathbf y^n}$; $\frac{\partial\mathbf y^n}{\partial\mathbf y^{n-1}}=\dot\sigma^nW^n$ | 06 |
| 6 | Momentum $\Delta\vec w^{(t)}=-\varepsilon\sum_{k\in T}\nabla E_k+\alpha\Delta\vec w^{(t-1)}$ | 08 |
| 7 | Power $\epsilon_0/(1+t/s)$; exponential $\epsilon_0\,0.1^{t/s}$ | 08 |
| 8 | AdaGrad $\Delta w=-\frac{\eta}{\sqrt r+\delta}g$, $r\leftarrow r+g^2$; RMSProp $r\leftarrow\rho r+(1-\rho)g^2$ ($\rho=0.9,\eta=0.001$) | 08 |
| 9 | LeCun $\mathcal N(0,\frac1n)$, $U(\pm\sqrt{3/n})$; Glorot $\mathcal N(0,\frac2{m+n})$, $U(\pm\sqrt{6/(m+n)})$; He $\mathcal N(0,\frac2n)$ | 09 |
| 10 | BN $\hat z_{ki}=(z_{ki}-\mu_i)/s_i$, output $\vec\gamma\hat z_k+\vec\delta$ | 10 |
| 11 | Dropout $\xi_j=\sum_iw_{ji}(r_iy_i)$, $r_i\sim\mathrm{Bern}(\frac12)$; inference ×½ | 10 |
| 12 | Weight decay $w\leftarrow(1-\zeta)w-\varepsilon\,\partial E/\partial w$ ⇔ $E+\frac\zeta{2\varepsilon}\vec w\cdot\vec w$ | 10 |
| 13 | CNN: $\frac{\partial E_k}{\partial w_{ji}}=\sum_{r\ell\in[ji]}\frac{\partial E_k}{\partial y_r}\sigma'_r(\xi_r)y_\ell$; max-pool routing | 11 |
| 14 | Saliency $\lvert\partial\xi_i/\partial y_k\rvert$; Taylor $\xi_i(\vec x')\approx\xi_i(\vec x)+\frac{\partial\xi_i}{\partial X}(\vec x'-\vec x)$ | 12 |
| 15 | GradCAM $\alpha_i^\ell=\frac1{\lvert F^\ell\rvert}\sum_{j\in F^\ell}\frac{\partial\xi_i}{\partial y_j}$, $M_i^L=\mathrm{ReLU}(\sum_\ell\alpha_i^\ell F^\ell(I))$ | 12 |
| 16 | LIME $\mathcal T=\{(\vec x_h,\xi_i(I[\vec x_h]))\}$, ADALINE, $\pi_k=\exp(-(1-\sqrt{1-s_k/\ell})^2/2\nu^2)$ | 12 |
| 17 | RNN $\vec h_t=\sigma(U\vec x_t+W\vec h_{t-1})$, $\vec y_t=\sigma(V\vec h_t)$; BPTT | 13 |
| 18 | Attention $e_{ij}=\vec q_i\cdot\vec k_j$, $\alpha=\operatorname{softmax}(e/\sqrt{d})$, $\vec y_i=\sum_j\alpha_{ij}\vec v_j$ | 14 |
| 19 | PE $\sin(k/n^{2i/d})$, $\cos(k/n^{2i/d})$; LN $\gamma(\vec x-\mu)/\sigma+\beta$; GPT loss $-\sum\log\vec y_\ell[a_{\ell+1}]$ | 14 |

## C. Rapid-fire facts (cover the right side)

| Question | Answer |
|---|---|
| Layer numbering of an MLP | input layer = 0; "three-layer" = 2 hidden + output |
| $w_{ji}$ direction | from $i$ to $j$ |
| Neuron count < ? for Turing-equivalent RNN (no need to memorize) | < 1000 (r.e. languages); halting undecidable from 25 neurons |
| Rational vs real weights | Turing-equivalent vs super-Turing |
| Typical minibatch sizes | powers of 2, 32–256 (16 for large models) |
| AdaGrad typical $\eta$, $\delta$ | $0.01$, $10^{-8}$ |
| SELU constants | $\lambda\approx1.050$, $\alpha\approx1.673$ |
| Init ↔ activation | Glorot: none/tanh/logistic/softmax; He: ReLU/leaky; LeCun: SELU |
| Géron ranking | SELU > ELU > leakyReLU > ReLU > tanh > logistic |
| Data split for early stopping | 60 / 20 / 20 (train / val / test) |
| Bagging distinct fraction | $1-1/e\approx63\%$ |
| ILSVRC | 1.2M train, 1000 classes, top-5; GoogLeNet 22 layers 93.33%; ResNet 152 layers 3.57% error |
| MNIST CNN errors | 33 / 10 000 |
| GradCAM layer | last convolutional layer |
| LIME targets | the network's own $\xi_i$ on perturbed subimages (not true labels) |
| Why CE beats SE with sigmoid | SE gradient has the factor $y(1-y)$, which vanishes in saturation |
| Why ReLU needs He | ReLU halves the second moment, so double the variance |
| GPT vs GPT-2 | GPT-2 moves LN to the input of each sub-block (pre-LN) |
| $\odot$ in multi-head slide | concatenation |
| Masked attention | $e_{ij}=-\infty$ for future positions, so $\alpha_{ij}=0$ |

## D. "Explain the intuition" prompts (say 3–4 sentences out loud)

> [!question]- Why does momentum help?
> It averages past steps exponentially, so oscillating gradient components cancel and consistent ones accumulate (up to $1/(1-\alpha)$×). Zig-zagging in narrow valleys is damped.

> [!question]- Why do deep nets / RNNs suffer from vanishing gradients?
> Backprop multiplies one factor $\sigma'w$ per layer (or time step). Products of many factors <1 vanish and >1 explode. Fixes: good init (LeCun/Glorot/He), ReLU, batch/layer norm, residual connections, LSTM, attention.

> [!question]- Why weight sharing in CNNs?
> Features should be detected anywhere (spatial invariance). Sharing gives far fewer parameters (e.g. 78 instead of ~1.36M in the slide-176 net), which helps generalization and makes very deep networks possible.

> [!question]- Why is dropout a regularizer?
> Each step trains a random sub-network, so neurons cannot co-adapt. It approximates averaging an exponential ensemble of networks with shared weights.

> [!question]- What does a self-attention layer compute, in words?
> Each position forms a query, compares it with every position's key (scaled dot product + softmax), and outputs the corresponding weighted average of the values: content-based mixing of information across the whole sequence in one step.
