---
course: PV021 Neural Networks
topic: Formal neuron, linear separation, geometry
slides: 17–24
exam: "Know all definitions formally + draw & explain the geometric picture"
tags: [pv021, neuron, geometry, exam]
---

# 01 · Formal Neuron and Its Geometry (slides 17–24)

> [!abstract] What the exam wants
> - Formal definitions of the neuron **with and without bias** in mathematical notation.
> - The **geometric interpretation** of a neuron, which you must be able to **draw** (slide 20).
> - Why a single neuron cannot compute XOR.

Up: [[PV021-00 Index]] · Next: [[PV021-02 Network Architecture, Activity, Learning]]

---

## 1. Formal neuron without bias (slide 17)

```
                 y
                 ▲
               ( σ )      y = σ(ξ)
               ( ξ )      ξ = Σ wᵢ xᵢ
             ↗  ↑  ↖
          w₁   w₂ … wₙ
          x₁   x₂ … xₙ
```

> [!definition] Formal neuron (without bias)
> - $x_1,\dots,x_n \in \mathbb{R}$ are the **inputs**
> - $w_1,\dots,w_n \in \mathbb{R}$ are the **weights**
> - $\xi$ is the **inner potential**; almost always
> $$\xi = \sum_{i=1}^{n} w_i x_i$$
> - $y$ is the **output**, $y = \sigma(\xi)$, where $\sigma$ is the **activation function**, e.g. the *unit step function*
> $$\sigma(\xi)=\begin{cases}1 & \xi \ge h\\ 0 & \xi < h\end{cases}$$
> where $h\in\mathbb{R}$ is the **threshold**.

## 2. Formal neuron with bias (slide 18)

The threshold is replaced by an extra constant input.

> [!definition] Formal neuron (with bias)
> - $x_0 = 1, x_1,\dots,x_n\in\mathbb{R}$ are the **inputs** ($x_0$ is the constant "formal" input)
> - $w_0, w_1,\dots,w_n\in\mathbb{R}$ are the **weights**; $w_0$ is the **bias**
> - inner potential
> $$\xi = w_0 + \sum_{i=1}^{n} w_i x_i \;=\; \sum_{i=0}^{n} w_i x_i$$
> - output $y=\sigma(\xi)$, e.g. the unit step
> $$\sigma(\xi)=\begin{cases}1 & \xi \ge 0\\ 0 & \xi < 0\end{cases}$$
> - **Substitution:** $x_0 = 1$, $w_0 = -h$.

> [!tip] Why both are equivalent
> $\sum_{i=1}^n w_i x_i \ge h \iff -h + \sum_{i=1}^n w_i x_i \ge 0 \iff w_0 + \sum_{i=1}^n w_i x_i \ge 0$ with $w_0=-h$.
> So "threshold $h$" equals "bias $w_0=-h$ on a constant input $x_0 = 1$".

---

## 3. Neuron and linear separation (slide 19)

The inner potential
$$\xi = w_0 + \sum_{i=1}^n w_i x_i$$
defines a **separating hyperplane** $\xi = 0$ in the $n$-dimensional **input space**: a line in 2D, a plane in 3D, and so on.
- Points with $\xi > 0$ lie on one side, points with $\xi<0$ on the other.
- With the unit step activation the neuron outputs **1 on the side $\xi \ge 0$** and **0 on the side $\xi<0$**.

## 4. Neuron geometry: the picture you must draw (slide 20)

```
 x₂ ▲
    │ ╲                                   ξ(x') = w₀ + Σ wᵢ x'ᵢ > 0   (y = 1)
    │   ╲                    ● x'
    │     ╲               ⋰  ┆  distance of x' to H:
    │       ╲          ⋰     ┆      |w₀ + Σ wᵢ x'ᵢ|
    │   w⃗ ↗   ╲     ⋰        ┆     ─────────────────
    │    ↗      ╲ ⋰                  √(Σ wᵢ²)
    │  ↗  D       ╲
    │↗              ╲   H :  w₀ + Σ wᵢ xᵢ = 0
    O─────────────────╲──────────────────────▶ x₁
         ● x̄            ╲
   w₀ + Σ wᵢ x̄ᵢ < 0       ╲
       (y = 0)
                         D = −w₀ / √(Σ wᵢ²)   (signed distance of H from the origin, measured along w⃗)
```

How to draw it (in the exam, step by step):
1. Draw the axes and the hyperplane (a line in 2D) $H: w_0 + \sum_i w_i x_i = 0$.
2. Draw the **weight vector** $\vec w = (w_1,\dots,w_n)$ from the origin. It is **perpendicular (normal) to $H$** and points towards the half-space where $\xi > 0$.
3. Mark the distance of $H$ from the origin: $D = \dfrac{-w_0}{\|\vec w\|}$, where $\|\vec w\| = \sqrt{\sum_{i=1}^n w_i^2}$.
4. Mark a point $\vec x'$ with $\xi>0$ and a point $\bar x$ with $\xi<0$. The distance of $\vec x'$ from $H$ is $\dfrac{|w_0 + \sum_i w_i x'_i|}{\sqrt{\sum_i w_i^2}}$.

> [!note] Proofs of the geometric facts (so you can explain them)
> **$\vec w$ is normal to $H$.** Take $\vec a,\vec b \in H$. Then $w_0 + \vec w\cdot\vec a = 0 = w_0 + \vec w\cdot \vec b$, so $\vec w\cdot(\vec a - \vec b) = 0$. Hence $\vec w$ is orthogonal to every direction lying in $H$.
>
> **Signed distance of a point.** Write any point as $\vec x' = \vec x_H + r\,\frac{\vec w}{\|\vec w\|}$, with $\vec x_H \in H$. Then
> $$\xi(\vec x') = w_0 + \vec w\cdot \vec x_H + r\,\frac{\vec w\cdot\vec w}{\|\vec w\|} = 0 + r\|\vec w\| \;\Rightarrow\; r = \frac{w_0 + \sum_i w_i x'_i}{\|\vec w\|}.$$
> So $\xi(\vec x')$ is **proportional to the signed distance** of $\vec x'$ from $H$. It is positive in the direction of $\vec w$.
>
> **Distance of $H$ from the origin.** Put $\vec x' = \vec 0$: $r = w_0/\|\vec w\|$ is the signed distance of the origin from $H$. Equivalently, $H$ lies at $D = -w_0/\|\vec w\|$ from the origin along $\vec w$. Check: the point $t\,\vec w/\|\vec w\|$ lies on $H$ iff $w_0 + t\|\vec w\| = 0$ iff $t = -w_0/\|\vec w\|$. ✔

> [!tip] Intuition
> - The weights $\vec w$ set the **orientation** of the hyperplane.
> - The bias $w_0$ **shifts** it: $|w_0|/\|\vec w\|$ is its distance from the origin.
> - Scaling all weights by $c>0$ leaves $H$ and the step-neuron's output unchanged; only $|\xi|$ scales.
> - The neuron is a **linear classifier**. It answers: "on which side of $H$ is the input?"

---

## 5. Example: classifying letters A vs B (slides 21–23)

- Input: an $8\times 8$ binary image, so $n = 8\cdot 8 = 64$ inputs (black pixel $\approx 1$, white $\approx 0$).
- The neuron outputs $1/0$ according to A/B.
- Slide 23: a **red** hyperplane $w_0 + \sum w_i x_i = 0$ classifies the points incorrectly. A **green** hyperplane $\bar w_0 + \sum \bar w_i x_i = 0$ classifies them correctly; it can be the result of a correction made by a learning algorithm.
- *Learning = moving the hyperplane by changing the weights.*

## 6. XOR is not linearly separable (slide 24)

| $x_1$ | $x_2$ | XOR |
|---|---|---|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

**No line separates the ones from the zeros.**

> [!note]- Proof (worth knowing)
> Suppose a step neuron with weights $w_0,w_1,w_2$ computes XOR. Then
> - $(0,0)\mapsto 0$: $w_0 < 0$
> - $(1,1)\mapsto 0$: $w_0 + w_1 + w_2 < 0$
> - $(1,0)\mapsto 1$: $w_0 + w_1 \ge 0$
> - $(0,1)\mapsto 1$: $w_0 + w_2 \ge 0$
>
> Adding the last two gives $2w_0 + w_1 + w_2 \ge 0$. Adding the first two gives $2w_0 + w_1 + w_2 < 0$. Contradiction. ∎
>
> (Slide-figure typo: both off-diagonal corners are labelled $(0,1)$. With $x_1$ on the vertical axis, the top-left corner is $(1,0)$.)

---

## Rehearsal

> [!question]- Define a formal neuron with bias (all symbols).
> Inputs $x_0=1,x_1,\dots,x_n\in\mathbb R$ and weights $w_0,\dots,w_n\in\mathbb R$. Inner potential $\xi = w_0+\sum_{i=1}^n w_ix_i$. Output $y=\sigma(\xi)$, where $\sigma$ is the activation function, e.g. the unit step ($1$ if $\xi\ge 0$, else $0$). The bias $w_0=-h$ replaces the threshold $h$.

> [!question]- What is the geometric meaning of $\vec w$ and $w_0$?
> $\vec w=(w_1,\dots,w_n)$ is the normal vector of the hyperplane $\xi=0$ and points to the side $\xi>0$. The hyperplane's signed distance from the origin along $\vec w$ is $-w_0/\|\vec w\|$.

> [!question]- Distance of a point $\vec x'$ from the separating hyperplane?
> $\dfrac{|w_0+\sum_{i=1}^n w_ix'_i|}{\sqrt{\sum_{i=1}^n w_i^2}}$

> [!question]- Why can't one neuron compute XOR?
> XOR is not linearly separable. The four inequalities $w_0<0$, $w_0+w_1+w_2<0$, $w_0+w_1\ge0$, $w_0+w_2\ge 0$ are contradictory: summing pairs gives $2w_0+w_1+w_2$ both $\ge 0$ and $<0$.

> [!question]- How many inputs does the A/B letter neuron have?
> $n = 8\cdot 8 = 64$ binary pixel inputs (plus the bias input $x_0=1$).
