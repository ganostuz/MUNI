---
course: PV021 Neural Networks
topic: Momentum, learning-rate search & schedules, AdaGrad, RMSProp
slides: 120–130
exam: "Detailed knowledge incl. formulae (momentum, AdaGrad, RMSProp). Slide 125: know that scheduling exists + be able to demonstrate one schedule"
tags: [pv021, optimization, momentum, adagrad, rmsprop, exam]
---

# 08 · Momentum, Learning Rate, AdaGrad, RMSProp (slides 120–130)

Up: [[PV021-00 Index]] · Prev: [[PV021-07 Gradient Descent Issues and Minibatches]] · Next: [[PV021-09 Activations, Preprocessing, Initialization]]

---

## 1. Momentum (slides 120–122)

> [!warning] The problem (slide 120)
> In a long narrow valley, $\nabla E(\vec w^{(t)})$ **constantly changes direction** and zig-zags across the valley, while the error still decreases steadily. Progress along the valley floor is slow.

> [!definition] Momentum update
> In every step, **add the change made in the previous step**, weighted by a factor $\alpha$:
> $$\Delta\vec w^{(t)} = -\varepsilon(t)\cdot\sum_{k\in T}\nabla E_k\big(\vec w^{(t)}\big) + \alpha\cdot\Delta\vec w^{(t-1)},\qquad 0<\alpha<1$$
> (with $\Delta\vec w^{(-1)} = \vec 0$).

> [!algorithm] SGD with momentum (slide 122)
> - Weights in $\vec w^{(0)}$ are randomly initialized to values close to $0$.
> - In step $t+1$ ($t=0,1,2,\dots$): choose (randomly) a minibatch $T\subseteq\{1,\dots,p\}$ and compute
> $$\vec w^{(t+1)} = \vec w^{(t)} + \Delta\vec w^{(t)},\qquad \Delta\vec w^{(t)} = -\varepsilon(t)\cdot\sum_{k\in T}\nabla E_k\big(\vec w^{(t)}\big) + \alpha\,\Delta\vec w^{(t-1)}$$
> - $0<\varepsilon(t)\le1$ is the learning rate in step $t+1$.
> - $0<\alpha<1$ measures the **"influence" of the momentum**.
> - $\nabla E_k(\vec w^{(t)})$ is the gradient of the error of example $k$.
> - The random choice of minibatches is implemented by random shuffling and then taking minibatches sequentially.

> [!tip] Intuition (slide 121 picture), with properties checked numerically
> Unrolling with constant $\varepsilon$ and $\vec g^{(s)} = \sum_{k\in T_s}\nabla E_k(\vec w^{(s)})$:
> $$\Delta\vec w^{(t)} = -\varepsilon\sum_{s=0}^{t}\alpha^{t-s}\,\vec g^{(s)}$$
> This is an **exponentially decaying average of past gradients**.
> - Components that **oscillate** (change sign) **cancel out**: the zig-zag across the valley is damped.
> - Components that **consistently point the same way** add up and accelerate: if $\vec g$ is constant, $\Delta\vec w\to -\dfrac{\varepsilon}{1-\alpha}\vec g$. With $\alpha=0.9$ that is **10× the plain step**.
> - Physical picture: a heavy ball rolling down the error surface; $\alpha$ acts like (1 − friction).

---

## 2. Learning rate (slides 123–125)

> [!note] Learning-rate curves (slide 123): loss vs epoch
> - **Very high** learning rate: the loss explodes (goes up).
> - **High**: fast initial drop, then gets stuck on a plateau at a poor loss.
> - **Low**: slow, steady decrease.
> - **Good**: fast decrease to a low loss.

> [!note] Search for the learning rate (slide 124)
> - Use the settings from a successful solution of a similar problem as a **baseline**.
> - Search for the learning rate using learning monitoring: try values from small (e.g. $0.001$) to large ($0.1$), possibly **multiplying by 2** each time; train for several epochs and observe the learning curves (cf. cross-validation).

> [!definition] Adaptive learning rate: schedules (slide 125)
> You do not need all of them, but **must be able to demonstrate one**.
> - **Power scheduling:** $\varepsilon(t) = \dfrac{\epsilon_0}{1+t/s}$, where $\epsilon_0$ is the initial learning rate and $s$ is a constant. After $s$ steps the rate is $\epsilon_0/2$, after $2s$ steps $\epsilon_0/3$, etc.
> - **Exponential scheduling:** $\varepsilon(t) = \epsilon_0\cdot 0.1^{t/s}$. It decays faster than power scheduling: divided by 10 every $s$ steps.
> - **Piecewise constant scheduling:** a constant rate for a number of steps/epochs, then a smaller rate, and so on.
> - **1cycle scheduling:** start by increasing the initial rate $\epsilon_0$ **linearly** to $\epsilon_1$ (approx. $\epsilon_1 = 10\epsilon_0$) halfway through training; then decrease linearly from $\epsilon_1$ back to $\epsilon_0$; finish by dropping the rate by several orders of magnitude (still linearly). According to a 2018 paper by Leslie Smith, this may converge much faster (100 epochs vs 800 epochs on CIFAR10).

> [!example] Demonstrating power scheduling with $\epsilon_0 = 0.1$, $s = 100$
> | $t$ | 0 | 100 | 200 | 300 | 900 |
> |---|---|---|---|---|---|
> | $\varepsilon(t) = 0.1/(1+t/100)$ | 0.1 | 0.05 | 0.0333 | 0.025 | 0.01 |
> For comparison, exponential with the same $\epsilon_0, s$: $0.1,\ 0.01,\ 0.001,\ 10^{-4},\dots$

---

## 3. AdaGrad (slides 126–127)

> [!note] Motivation (slide 126)
> So far all schedules were **fixed** and the same for all weights. It is better to have
> - **larger rates for weights with smaller updates**,
> - **smaller rates for weights with larger updates**.
>
> AdaGrad uses **individually adapting learning rates for each weight**.

> [!definition] SGD with AdaGrad (slide 127)
> - Weights in $\vec w^{(0)}$ are randomly initialized to values close to $0$.
> - In step $t+1$: choose (randomly) a minibatch $T\subseteq\{1,\dots,p\}$ and compute
> $$w_{ji}^{(t+1)} = w_{ji}^{(t)} + \Delta w_{ji}^{(t)},\qquad \Delta w_{ji}^{(t)} = -\frac{\eta}{\sqrt{r_{ji}^{(t)}}+\delta}\cdot\sum_{k\in T}\frac{\partial E_k}{\partial w_{ji}}\big(\vec w^{(t)}\big)$$
> $$r_{ji}^{(t)} = r_{ji}^{(t-1)} + \Bigg(\sum_{k\in T}\frac{\partial E_k}{\partial w_{ji}}\big(\vec w^{(t)}\big)\Bigg)^2\qquad(r_{ji}^{(-1)} = 0)$$
> - $\eta$ is a constant expressing the influence of the learning rate, typically $0.01$.
> - $\delta>0$ is a smoothing term (typically $10^{-8}$) avoiding division by $0$.
> - (On the slide the square root covers only $r_{ji}^{(t)}$; $\delta$ is added outside the root.)

> [!tip] Intuition
> $r_{ji}$ accumulates the **sum of squared gradients** of weight $w_{ji}$ over the whole history. Weights with a history of large gradients get a small effective rate $\eta/\sqrt{r_{ji}}$; rarely updated weights keep a large one.

---

## 4. RMSProp (slides 128–129)

> [!warning] The main disadvantage of AdaGrad (slide 128)
> Gradients **accumulate throughout the whole learning process**, so $r_{ji}$ only grows. If learning must get over several "hills" before settling in a deep "valley", the weight updates become **far too small** before getting there.
> **RMSProp** uses an **exponentially decaying average** to discard history from the extreme past. It can then converge rapidly after finding a convex bowl, as if it were an instance of AdaGrad initialized within that bowl.

> [!definition] SGD with RMSProp (slide 129)
> Same as AdaGrad except for the update of $r_{ji}$:
> $$\Delta w_{ji}^{(t)} = -\frac{\eta}{\sqrt{r_{ji}^{(t)}}+\delta}\cdot\sum_{k\in T}\frac{\partial E_k}{\partial w_{ji}}\big(\vec w^{(t)}\big)$$
> $$r_{ji}^{(t)} = \rho\, r_{ji}^{(t-1)} + (1-\rho)\Bigg(\sum_{k\in T}\frac{\partial E_k}{\partial w_{ji}}\big(\vec w^{(t)}\big)\Bigg)^2$$
> - $\eta$: a constant expressing the influence of the learning rate. **Hinton suggests $\rho=0.9$ and $\eta=0.001$.**
> - $\delta>0$: a smoothing term (typically $10^{-8}$).

> [!tip] AdaGrad vs RMSProp in one line
> AdaGrad: $r \leftarrow r + g^2$ (a sum, which only grows). RMSProp: $r\leftarrow\rho r + (1-\rho)g^2$ (a moving average, which forgets). With $\rho=0.9$ the memory is roughly the last $1/(1-\rho) = 10$ steps.

---

## 5. Other optimization methods (slide 130)

- There are more methods, e.g. **AdaDelta** and **Adam** (= **RMSProp combined with momentum**).
- Which algorithm should one choose? **There is currently no consensus.**
- According to a recent study, the family of algorithms with **adaptive learning rates** (represented by RMSProp and AdaDelta) performed fairly robustly; no single best algorithm has emerged.
- The most popular algorithms in active use: SGD, SGD with momentum, RMSProp, RMSProp with momentum, AdaDelta, and Adam.
- The choice seems to depend largely on the **user's familiarity** with the algorithm.

> [!note]- Extra (not on the slides): Adam's standard formulas
> With gradient $g_t$: $m_t = \beta_1m_{t-1}+(1-\beta_1)g_t$ (momentum), $v_t = \beta_2v_{t-1}+(1-\beta_2)g_t^2$ (RMSProp). Bias correction $\hat m_t = m_t/(1-\beta_1^t)$, $\hat v_t = v_t/(1-\beta_2^t)$. Update $w\leftarrow w - \eta\,\hat m_t/(\sqrt{\hat v_t}+\delta)$. Defaults: $\beta_1=0.9$, $\beta_2=0.999$, $\eta=0.001$, $\delta=10^{-8}$.

---

## Rehearsal

> [!question]- Write the SGD-with-momentum update. What does $\alpha$ do?
> $\Delta\vec w^{(t)}=-\varepsilon(t)\sum_{k\in T}\nabla E_k(\vec w^{(t)})+\alpha\Delta\vec w^{(t-1)}$, $\vec w^{(t+1)}=\vec w^{(t)}+\Delta\vec w^{(t)}$, with $0<\alpha<1$. $\alpha$ is the influence of the previous step: it damps oscillations and accelerates consistent directions (up to $1/(1-\alpha)$×).

> [!question]- Demonstrate one learning-rate schedule.
> Power: $\varepsilon(t)=\epsilon_0/(1+t/s)$, giving $\epsilon_0/2$ after $s$ steps and $\epsilon_0/3$ after $2s$. (Or exponential $\epsilon_0\,0.1^{t/s}$, piecewise constant, 1cycle.)

> [!question]- Write AdaGrad.
> $\Delta w_{ji}^{(t)}=-\frac{\eta}{\sqrt{r_{ji}^{(t)}}+\delta}\sum_{k\in T}\frac{\partial E_k}{\partial w_{ji}}(\vec w^{(t)})$ with $r_{ji}^{(t)}=r_{ji}^{(t-1)}+\big(\sum_{k\in T}\frac{\partial E_k}{\partial w_{ji}}(\vec w^{(t)})\big)^2$; $\eta\approx0.01$, $\delta\approx10^{-8}$.

> [!question]- Write RMSProp and say why it improves on AdaGrad.
> Same update, but $r_{ji}^{(t)}=\rho r_{ji}^{(t-1)}+(1-\rho)(\sum_{k\in T}\partial E_k/\partial w_{ji})^2$; Hinton suggests $\rho=0.9$, $\eta=0.001$. The exponential decay forgets old gradients, so the rates don't shrink to zero after crossing "hills".

> [!question]- What is Adam (per the slides)?
> RMSProp combined with momentum.

> [!question]- Describe 1cycle.
> Increase linearly from $\epsilon_0$ to $\epsilon_1\approx10\epsilon_0$ until mid-training, decrease linearly back to $\epsilon_0$, then drop by several orders of magnitude (linearly). Smith 2018: 100 vs 800 epochs on CIFAR10.
