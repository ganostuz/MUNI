---
course: PV021 Neural Networks
topic: Expressive power (Boolean functions, separation, approximation, computability)
slides: 41–54
exam: "Complete proofs may be demanded (slide 44); basic idea of slides 47–48; claims of slide 52 (no exact numbers)"
tags: [pv021, theory, proofs, exam]
---

# 03 · Expressive Power of Neural Networks (slides 41–54)

Up: [[PV021-00 Index]] · Prev: [[PV021-02 Network Architecture, Activity, Learning]] · Next: [[PV021-04 MLP and Backpropagation]]

> [!abstract] Exam focus
> - **Full proof** that two-layer step networks compute every Boolean function (slide 44).
> - **Basic idea** of the ReLU universality construction (slides 47, 48: the "hat" functions).
> - The **claims** about computability (slides 51–52). Exact neuron counts are not required.

---

## 1. Boolean functions with single neurons (slides 42–43)

Activation: unit step $\sigma(\xi)=1$ iff $\xi\ge 0$. Inputs $x_i\in\{0,1\}$, bias input $x_0=1$.

| Function | bias $w_0$ | weights | why it works |
|---|---|---|---|
| $\mathrm{AND}(x_1,\dots,x_n)$ | $-n$ | all $1$ | $\sum x_i - n\ge 0 \iff$ all $x_i = 1$ |
| $\mathrm{OR}(x_1,\dots,x_n)$ | $-1$ | all $1$ | $\sum x_i - 1\ge 0\iff$ at least one $x_i=1$ |
| $\mathrm{NOT}(x_1)$ | $0$ | $-1$ | $-x_1\ge 0\iff x_1 = 0$ |

---

## 2. Theorem: two-layer MLPs compute all Boolean functions (slide 44)

> [!theorem] Theorem
> Let $\sigma$ be the unit step function. Two-layer MLPs in which every neuron has activation $\sigma$ can compute **all** functions of the form $F:\{0,1\}^n\to\{0,1\}$.

> [!proof]- Full proof (memorize)
> **Step 1 – a neuron that recognizes one vector.** For a vector $\vec v = (v_1,\dots,v_n)\in\{0,1\}^n$ define a neuron $N_{\vec v}$ with
> $$w_0 = -\sum_{i=1}^n v_i, \qquad w_i = \begin{cases} 1 & v_i = 1\\ -1 & v_i = 0\end{cases}$$
> **Claim:** $N_{\vec v}$ outputs $1$ iff its input is $\vec x = \vec v$.
>
> *Proof of claim.* For an input $\vec x\in\{0,1\}^n$:
> $$\xi = -\sum_i v_i + \sum_{i: v_i=1} x_i - \sum_{i:v_i=0} x_i = -\sum_{i:v_i=1}(1-x_i) - \sum_{i:v_i=0} x_i .$$
> Each term $(1-x_i)$ with $v_i=1$, and each term $x_i$ with $v_i=0$, equals $1$ exactly when $x_i\neq v_i$. Hence
> $$\xi = -\,\#\{i : x_i \ne v_i\} = -d_H(\vec x,\vec v)\quad\text{(minus the Hamming distance)}.$$
> So $\xi = 0$ (output $1$) iff $\vec x=\vec v$, and $\xi\le -1$ (output $0$) otherwise. ✔
>
> **Step 2 – combine with OR.** Take the hidden layer $\{N_{\vec v} : F(\vec v) = 1\}$ and connect all their outputs to an output neuron that computes **OR** (bias $-1$, weights $1$).
> - If $F(\vec x)=1$, then the neuron $N_{\vec x}$ is in the hidden layer and outputs 1, so the OR outputs 1.
> - If $F(\vec x)=0$, then every hidden $N_{\vec v}$ has $\vec v\neq \vec x$ and outputs 0, so the OR outputs 0.
>
> Hence the network computes $F$. It has two layers (hidden + output). ∎
>
> *Edge case:* if $F\equiv 0$ the hidden layer is empty. The OR neuron then has only its bias $-1$ and outputs $0$, so the construction still works.
>
> (The construction is a neural version of the **disjunctive normal form**. Each $N_{\vec v}$ is a minterm, and it may need up to $2^n$ hidden neurons.)

The construction was checked exhaustively for all $2^{8}=256$ functions $\{0,1\}^3\to\{0,1\}$.

---

## 3. Non-linear separation with step networks (slides 45–46)

Consider a **three-layer** network with unit step activations. It divides the input space into two subspaces according to the output (0/1):
1. The **first** (hidden) layer divides the input space into **half-spaces**.
2. The **second** layer can, for example, make **intersections** of half-spaces (AND), giving **convex sets**.
3. The **third** layer can, for example, make **unions** of convex sets (OR).

> [!note] Slide 46: "approximating" any reasonable set $A\subseteq\mathbb R^k$
> - Cover $A$ with **hypercubes** (squares in 2D, cubes in 3D, …).
> - Each hypercube $K$ can be separated by a **two-layer** network $N_K$: $2k$ half-space neurons ($a_i\le x_i$ and $x_i\le b_i$ for each coordinate) followed by an AND neuron. $N_K$ outputs 1 for points in $K$ and 0 elsewhere.
> - Finally, connect the outputs of the nets $N_K$ with $K\cap A\neq\emptyset$ to a neuron implementing **OR**.

---

## 4. Power of ReLU (slides 47–48)

> [!theorem] Setting (slide 47)
> A **two-layer** network with a **single input** and a **single output**. Hidden neurons use ReLU $\sigma(\xi)=\max(\xi,0)$. The output neuron is the identity $\sigma(\xi)=\xi$ (a linear model).
> 1. **Approximation:** for every continuous $f:[0,1]\to[0,1]$ and every $\varepsilon>0$ there is such a network computing $F:[0,1]\to\mathbb R$ with $|f(x)-F(x)|\le\varepsilon$ for all $x\in[0,1]$.
> 2. **Classification:** for every open $A\subseteq[0,1]$ there is such a network such that for "most" $x\in[0,1]$: $x\in A$ iff the network's output for $x$ is $>0$.

### The basic idea: building a "hat" from three ReLUs (slide 48, handwritten)

```
 hidden (input x, bias on constant input 1):       output (identity):
   red   r(x) = ReLU( s·x + ℓ )   bias  ℓ, weight s     y = 1·r − 1·g + 1·b
   gold  g(x) = ReLU( 2s·x )      bias  0, weight 2s
   blue  b(x) = ReLU( s·x − ℓ )   bias −ℓ, weight s

                ▲  y
                │        peak ℓ at x = 0
                │          /\
                │         /  \
                │        /    \
   ─────────────┴───────/──────\────────────▶ x
                    −ℓ/s   0   ℓ/s
```

Check of $y = r - g + b$ (also verified numerically):
- $x\le -\ell/s$: all three are $0$, so $y=0$.
- $-\ell/s\le x\le 0$: $y = sx+\ell$ (rising, slope $s$).
- $0\le x\le \ell/s$: $y = (sx+\ell) - 2sx = \ell - sx$ (falling, slope $-s$).
- $x\ge \ell/s$: $y = (sx+\ell) - 2sx + (sx-\ell) = 0$.

So $y(x) = \max(0,\ \ell - s|x|)$: a **triangle ("hat")** of height $\ell$ and half-width $\ell/s$. Replacing $x$ by $x - c$ (i.e. changing the biases) moves it to any center $c$.

**Approximation idea.** "Red = sum of black": a **sum of shifted and scaled hats** is a **piecewise-linear** function. A continuous $f$ on $[0,1]$ is uniformly continuous, so the piecewise-linear interpolation of $f$ on a fine enough grid is within $\varepsilon$ of $f$. That interpolation is a sum of hats (one per grid point, height $f(\text{grid point})$), and each hat costs 3 ReLU neurons. ∎ (idea)

> [!note]- Idea for the classification claim (slide 47, green text)
> Take a continuous $f$ where $f(x)$ is the **distance of $x$ to the boundary of $A$**, then uniformly approximate $f$ by the networks.
> One clean way to make it work: let $f(x)=\operatorname{dist}(x,[0,1]\setminus A)$. Because $A$ is open, $f>0$ exactly on $A$ and $f=0$ outside. Approximate $f-\varepsilon/2$ to within $\varepsilon/3$ by $F$. Then:
> - outside $A$: $F\le -\varepsilon/6<0$;
> - inside $A$ with $f(x)>\varepsilon$: $F>0$.
>
> Only points of $A$ within $\varepsilon$ of the complement can be misclassified, and their measure tends to $0$ as $\varepsilon\to0$, hence "most" $x$.

---

## 5. Cybenko's theorems: sigmoidal two-layer networks (slides 49–50)

> [!theorem] Cybenko 1989, informal (slide 49): classification
> Let $\sigma$ be a **continuous sigmoidal** function, i.e. $\sigma(x)\to 1$ for $x\to+\infty$ and $\sigma(x)\to 0$ for $x\to-\infty$.
> For every "reasonable" set $A\subseteq[0,1]^n$ there is a **two-layer network** (hidden neurons with activation $\sigma$, **linear output**) such that for "most" vectors $\vec v\in[0,1]^n$: $\vec v\in A$ iff the network output for $\vec v$ is $>0$.
> - "reasonable" = Lebesgue measurable
> - "most" = the set of incorrectly classified vectors has Lebesgue measure smaller than a given $\varepsilon>0$

> [!theorem] Cybenko 1989 (slide 50): function approximation
> Let $\sigma$ be a continuous sigmoidal function, i.e. **increasing**, with $\sigma(x)\to1$ ($x\to+\infty$) and $\sigma(x)\to0$ ($x\to-\infty$).
> For every **continuous** $f:[0,1]^n\to[0,1]$ and every $\varepsilon>0$ there is a function $F:[0,1]^n\to[0,1]$ computed by a **two-layer network** (hidden activation $\sigma$, linear output neurons) such that
> $$|f(\vec v) - F(\vec v)| < \varepsilon \quad\text{for every } \vec v\in[0,1]^n.$$

---

## 6. Neural networks and computability (slides 51–52)

> [!definition] Setting (slide 51)
> - **Recurrent** networks (containing cycles) with **real** weights in general.
> - One input neuron and one output neuron. The network computes $F:A\to\mathbb R$ where $A\subseteq\mathbb R$ is the set of inputs on which it stops.
> - **Parallel** activity rule: the outputs of all neurons are recomputed in every step.
> - Activation function (saturated linear):
> $$\sigma(\xi)=\begin{cases}1 & \xi\ge 1\\ \xi & 0\le\xi\le 1\\ 0 & \xi<0\end{cases}$$

> [!definition] Encoding words as numbers
> A word $\omega\in\{0,1\}^+$ is encoded as
> $$\delta(\omega) = \sum_{i=1}^{|\omega|}\frac{\omega(i)}{2^i} + \frac{1}{2^{|\omega|+1}}$$
> Example: $\omega = 11001$ gives $\delta(\omega) = \frac12+\frac1{2^2}+\frac1{2^5}+\frac1{2^6}$ $(=0.110011$ in binary $=0.796875)$.
> The final $1/2^{|\omega|+1}$ is an "end marker". It makes the encoding injective; for example $1$ and $10$ get different codes.

> [!definition] Recognizing a language (slide 52)
> A network **recognizes** $L\subseteq\{0,1\}^+$ if it computes $F:A\to\mathbb R$ ($A\subseteq \mathbb R$) such that
> $$\omega\in L \iff \delta(\omega)\in A \text{ and } F(\delta(\omega))>0 .$$

> [!important] Claims you must know (exact numbers not required)
> - **Recurrent networks with rational weights are equivalent to Turing machines.**
>   - For every **recursively enumerable** language $L\subseteq\{0,1\}^+$ there is a recurrent network with rational weights and fewer than 1000 neurons that recognizes $L$.
>   - The **halting problem is undecidable** for networks with at least 25 neurons and rational weights.
>   - There is a **"universal" network**, the equivalent of the universal Turing machine.
> - **Recurrent networks are super-Turing powerful** (with **real** weights): for **every** language $L\subseteq\{0,1\}^+$ (even non-r.e. ones) there is a recurrent network with fewer than 1000 neurons that recognizes $L$. Intuition: one real weight can encode infinitely many bits of information.

---

## 7. Summary of the theory (slides 53–54)

- All **Boolean functions** can be expressed by **two-layer** networks.
- **Two-layer** networks can **approximate any continuous function**.
- **Recurrent** networks are **at least as strong as Turing machines**.
- These results are **purely theoretical**: such networks are extremely huge and very hard to handcraft even for the simplest problems.
- Practically the most important advantages are **learning, generalization, robustness**.

| | Neural networks | "Classical" computers |
|---|---|---|
| Data | implicitly in weights | explicitly |
| Computation | naturally parallel | sequential, localized |
| Robustness | robust w.r.t. input corruption & damage | changing one bit may crash the computation |
| Precision | imprecise; recalls a training example "similar" to the input | (typically) precise |
| Programming | learning | manual |

---

## Rehearsal

> [!question]- State and prove the Boolean-function theorem.
> Two-layer step MLPs compute every $F:\{0,1\}^n\to\{0,1\}$. For each $\vec v$ with $F(\vec v)=1$ take $N_{\vec v}$ with $w_0=-\sum v_i$ and $w_i=\pm1$ ($+1$ iff $v_i=1$); its potential is $-d_H(\vec x,\vec v)$, so it fires iff $\vec x=\vec v$. OR all of them.

> [!question]- Which neurons compute AND, OR, NOT?
> AND: $w_0=-n$, all weights $1$. OR: $w_0=-1$, all weights $1$. NOT: $w_0=0$, weight $-1$.

> [!question]- Give three ReLUs whose combination is a hat function.
> $\mathrm{ReLU}(sx+\ell)-\mathrm{ReLU}(2sx)+\mathrm{ReLU}(sx-\ell) = \max(0,\ell-s|x|)$.

> [!question]- What does each layer of a 3-layer step network do geometrically?
> Layer 1: half-spaces. Layer 2: intersections of half-spaces (convex sets). Layer 3: unions of convex sets.

> [!question]- Cybenko's approximation theorem?
> For continuous sigmoidal (increasing, limits $0$ and $1$) $\sigma$, every continuous $f:[0,1]^n\to[0,1]$ and every $\varepsilon>0$ have a two-layer net (σ hidden, linear output) with $|f-F|<\varepsilon$ everywhere on $[0,1]^n$.

> [!question]- What is $\delta(\omega)$ and when does a network recognize $L$?
> $\delta(\omega)=\sum_{i=1}^{|\omega|}\omega(i)2^{-i}+2^{-(|\omega|+1)}$. The network recognizes $L$ iff ($\omega\in L\iff\delta(\omega)\in A\wedge F(\delta(\omega))>0$).

> [!question]- Rational vs real weights in recurrent networks?
> Rational weights: equivalent to Turing machines (r.e. languages, undecidable halting, universal network). Real weights: super-Turing (every language is recognizable).
