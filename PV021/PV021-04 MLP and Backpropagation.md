---
course: PV021 Neural Networks
topic: MLP formalism, gradient descent, backpropagation, SGD
slides: 83–97
exam: "Everything in great detail, all mathematical details and all proofs (slide 91 not required)"
tags: [pv021, backprop, mlp, proofs, exam]
---

# 04 · MLP Training: Gradient Descent and Backpropagation (slides 83–97)

Up: [[PV021-00 Index]] · Prev: [[PV021-03 Expressive Power and Computability]] · Next: [[PV021-05 Output Activations and Error Functions]]

> [!abstract] Exam focus
> Everything in great detail: the notation, the error function, batch and online GD, SGD, the **gradient formula and its full derivation (proof)**, the backpropagation order, and complexity.

---

## 1. MLP: architecture and notation (slides 83–84)

> [!definition] Notation (slide 84)
> - $X$ = set of **input** neurons, $Y$ = set of **output** neurons, $Z$ = set of **all** neurons ($X,Y\subseteq Z$).
> - Individual neurons are denoted by indices $i,j$, etc.
> - $\xi_j$ = inner potential of neuron $j$ **after the computation stops**.
> - $y_j$ = output of neuron $j$ **after the computation stops**.
> - Define $y_0 = 1$, the value of the formal unit input.
> - $w_{ji}$ = weight of the connection **from $i$ to $j$** (note the order: *to* $j$, *from* $i$).
>   In particular $w_{j0}$ is the weight from the formal unit input, i.e. the bias. (The slide writes $w_{j0}=-b_j$ with $b_j$ called the "bias", i.e. $b_j$ plays the role of the threshold, as with $w_0=-h$ in [[PV021-01 Formal Neuron and Geometry]].)
> - $j_{\leftarrow}$ = set of all $i$ such that $j$ is adjacent **from** $i$ (there is an arc **to $j$ from $i$**): the **predecessors** of $j$.
> - $j^{\rightarrow}$ = set of all $i$ such that $j$ is adjacent **to** $i$ (there is an arc **from $j$ to $i$**): the **successors** of $j$.

## 2. MLP activity (slide 85)

> [!definition] Activity
> - Inner potential of neuron $j$:
> $$\xi_j = \sum_{i\in j_\leftarrow} w_{ji}\,y_i$$
>   (the bias is included because $0\in j_\leftarrow$ and $y_0=1$).
> - Activation function $\sigma_j$ for neuron $j$ (arbitrary **differentiable**).
> - State of a non-input neuron $j\in Z\setminus X$ after the computation stops:
> $$y_j = \sigma_j(\xi_j)$$
> - $y_j$ depends on the configuration $\vec w$ and the input $\vec x$, so we sometimes write $y_j(\vec w,\vec x)$.
> - The network computes a function $\mathbb R^{|X|}\to\mathbb R^{|Y|}$. Layer-wise computation: first all input neurons are assigned the input values; in the $\ell$-th step all neurons of the $\ell$-th layer are evaluated.

## 3. MLP learning: training set and error (slide 86)

> [!definition] Training dataset and error function
> Training dataset
> $$\mathcal T = \Big\{\big(\vec x_k, \vec d_k\big)\ \Big|\ k=1,\dots,p\Big\}$$
> Each $\vec x_k\in\mathbb R^{|X|}$ is an input vector and each $\vec d_k\in\mathbb R^{|Y|}$ is the desired network output. For $j\in Y$, $d_{kj}$ is the desired output of neuron $j$ for input $\vec x_k$, so $\vec d_k = (d_{kj})_{j\in Y}$.
>
> **Error function** (squared error, one example of an error function):
> $$E(\vec w) = \sum_{k=1}^p E_k(\vec w), \qquad E_k(\vec w) = \frac12\sum_{j\in Y}\big(y_j(\vec w,\vec x_k) - d_{kj}\big)^2$$

## 4. Batch gradient descent (slide 87)

> [!definition] Batch algorithm (gradient descent)
> Compute a sequence of weight vectors $\vec w^{(0)},\vec w^{(1)},\vec w^{(2)},\dots$
> - Weights in $\vec w^{(0)}$ are **randomly initialized to values close to 0**.
> - In step $t+1$ ($t = 0,1,2,\dots$):
> $$w_{ji}^{(t+1)} = w_{ji}^{(t)} + \Delta w_{ji}^{(t)}, \qquad \Delta w_{ji}^{(t)} = -\varepsilon(t)\cdot\frac{\partial E}{\partial w_{ji}}\big(\vec w^{(t)}\big)$$
>   where $0<\varepsilon(t)\le 1$ is the **learning rate** in step $t+1$.
> - In vector form: $\vec w^{(t+1)} = \vec w^{(t)} - \varepsilon(t)\cdot\nabla E(\vec w^{(t)})$, since $\frac{\partial E}{\partial w_{ji}}(\vec w^{(t)})$ is a component of the gradient $\nabla E$.

> [!tip] Why minus the gradient?
> $\nabla E$ points in the direction of the steepest **increase** of $E$. A small step against it decreases $E$ to first order: $E(\vec w - \varepsilon\nabla E)\approx E(\vec w) - \varepsilon\|\nabla E\|^2$.

---

## 5. The gradient formula (slide 88), the core result

> [!important] Gradient of the error for an MLP
> For every $w_{ji}$:
> $$\frac{\partial E}{\partial w_{ji}} = \sum_{k=1}^p\frac{\partial E_k}{\partial w_{ji}}$$
> where for every $k=1,\dots,p$:
> $$\boxed{\frac{\partial E_k}{\partial w_{ji}} = \frac{\partial E_k}{\partial y_j}\cdot\sigma_j'(\xi_j)\cdot y_i}$$
> and for every $j\in Z\setminus X$:
> $$\boxed{\frac{\partial E_k}{\partial y_j} = y_j - d_{kj}}\qquad\text{for } j\in Y$$
> $$\boxed{\frac{\partial E_k}{\partial y_j} = \sum_{r\in j^\rightarrow}\frac{\partial E_k}{\partial y_r}\cdot\sigma_r'(\xi_r)\cdot w_{rj}}\qquad \text{for } j\in Z\setminus(Y\cup X)$$
> (Here all $y_j$ are in fact $y_j(\vec w,\vec x_k)$.)

### 5.1 Derivation (proof), slides 89–90

> [!proof] Part 1: derivative w.r.t. a weight (slide 89)
> Fix $k$ and a weight $w_{ji}$. $E_k$ depends on $w_{ji}$ only through $\xi_j$, and $\xi_j$ influences $E_k$ only through $y_j$. By the chain rule:
> $$\frac{\partial E_k}{\partial w_{ji}} = \frac{\partial E_k}{\partial y_j}\cdot\frac{\partial y_j}{\partial w_{ji}} = \frac{\partial E_k}{\partial y_j}\cdot\frac{\partial y_j}{\partial \xi_j}\cdot\frac{\partial \xi_j}{\partial w_{ji}} = \frac{\partial E_k}{\partial y_j}\cdot\sigma_j'(\xi_j)\cdot y_i$$
> since
> $$\frac{\partial y_j}{\partial\xi_j} = \frac{\partial\big(\sigma_j(\xi_j)\big)}{\partial \xi_j} = \sigma_j'(\xi_j), \qquad \frac{\partial\xi_j}{\partial w_{ji}} = \frac{\partial\left(\sum_{r\in j_\leftarrow} w_{jr}y_r\right)}{\partial w_{ji}} = y_i .$$
> (In the last sum only the term $r=i$ depends on $w_{ji}$; the $y_r$ are outputs of lower layers and do not depend on $w_{ji}$.)

> [!proof] Part 2: derivative w.r.t. an output (slide 90)
> **Output neuron** $j\in Y$:
> $$\frac{\partial E_k}{\partial y_j} = \frac{\partial\left(\frac12\sum_{r\in Y}(y_r - d_{kr})^2\right)}{\partial y_j} = y_j - d_{kj}$$
> (only the term $r=j$ depends on $y_j$; the $\frac12$ cancels the $2$).
>
> **Hidden neuron** $j\notin Y$: $y_j$ influences $E_k$ only through the neurons $r\in j^\rightarrow$ (all of them in the next layer). By the multivariate chain rule:
> $$\frac{\partial E_k}{\partial y_j} = \sum_{r\in j^\rightarrow}\frac{\partial E_k}{\partial y_r}\cdot\frac{\partial y_r}{\partial y_j} = \sum_{r\in j^\rightarrow}\frac{\partial E_k}{\partial y_r}\cdot\frac{\partial y_r}{\partial\xi_r}\cdot\frac{\partial \xi_r}{\partial y_j} = \sum_{r\in j^\rightarrow}\frac{\partial E_k}{\partial y_r}\cdot\sigma_r'(\xi_r)\cdot w_{rj}$$
> since
> $$\frac{\partial y_r}{\partial\xi_r} = \sigma_r'(\xi_r),\qquad \frac{\partial\xi_r}{\partial y_j} = \frac{\partial\left(\sum_{s\in r_\leftarrow} w_{rs}y_s\right)}{\partial y_j} = w_{rj}. \qquad\blacksquare$$

> [!tip] Memory hook: "error signal × local slope × incoming activation"
> $\dfrac{\partial E_k}{\partial w_{ji}} = \underbrace{\dfrac{\partial E_k}{\partial y_j}}_{\text{how much } j \text{ matters}}\cdot\underbrace{\sigma'_j(\xi_j)}_{\text{local slope}}\cdot\underbrace{y_i}_{\text{signal on the wire}}$.
> Backward pass: a neuron's error signal is the **weighted sum** of its successors' (error × slope), using the **same weights** $w_{rj}$ as the forward pass, but traversed in reverse.

### 5.2 Logistic sigmoid special case (slide 91, *not required*, but used later)

If $\sigma_j(\xi_j) = \frac1{1+e^{-\xi_j}}$ for all $j$, then $\sigma_j'(\xi_j) = y_j(1-y_j)$. Proof: $\sigma' = \frac{e^{-\xi}}{(1+e^{-\xi})^2} = \frac{1}{1+e^{-\xi}}\cdot\frac{e^{-\xi}}{1+e^{-\xi}} = \sigma(1-\sigma)$. With steepness $\lambda$: $\sigma' = \lambda\sigma(1-\sigma)$. Hence
$$\frac{\partial E_k}{\partial y_j} = \sum_{r\in j^\rightarrow}\frac{\partial E_k}{\partial y_r}\cdot y_r(1-y_r)\cdot w_{rj}\quad (j\in Z\setminus(Y\cup X)).$$

---

## 6. Computing the gradient: the algorithm (slides 92–93)

> [!algorithm] Compute $\frac{\partial E}{\partial w_{ji}} = \sum_{k=1}^p\frac{\partial E_k}{\partial w_{ji}}$
> Initialize $\mathcal E_{ji} := 0$. (By the end of the computation $\mathcal E_{ji} = \frac{\partial E}{\partial w_{ji}}$.)
> For every $k = 1,\dots,p$ do:
> 1. **Forward pass:** compute $y_j = y_j(\vec w,\vec x_k)$ for all $j\in Z$.
> 2. **Backward pass:** compute $\frac{\partial E_k}{\partial y_j}$ for all $j\in Z$ using backpropagation (below).
> 3. Compute $\frac{\partial E_k}{\partial w_{ji}}$ for all $w_{ji}$ by $\frac{\partial E_k}{\partial w_{ji}} := \frac{\partial E_k}{\partial y_j}\cdot\sigma_j'(\xi_j)\cdot y_i$.
> 4. $\mathcal E_{ji} := \mathcal E_{ji} + \frac{\partial E_k}{\partial w_{ji}}$.
>
> The resulting $\mathcal E_{ji}$ equals $\frac{\partial E}{\partial w_{ji}}$.

> [!algorithm] Backpropagation: compute $\frac{\partial E_k}{\partial y_j}$ for all $j\in Z$ (slide 93)
> - If $j\in Y$: $\frac{\partial E_k}{\partial y_j} = y_j - d_{kj}$.
> - If $j\in Z\setminus(Y\cup X)$, $j$ is in the $\ell$-th layer, and $\frac{\partial E_k}{\partial y_r}$ has already been computed for all neurons $r$ in layer $\ell+1$:
> $$\frac{\partial E_k}{\partial y_j} = \sum_{r\in j^\rightarrow}\frac{\partial E_k}{\partial y_r}\cdot\sigma_r'(\xi_r)\cdot w_{rj}$$
>   This works because all $r\in j^\rightarrow$ belong to layer $\ell+1$. So we go **layer by layer from the output down**.

## 7. Complexity of the batch algorithm (slide 94)

> [!theorem] Claim
> Computing $\frac{\partial E}{\partial w_{ji}}(\vec w^{(t-1)})$ takes time **linear in the size of the network times the size of the training set**, assuming unit cost of operations, including computing $\sigma_r'(\xi_r)$ for a given $\xi_r$.

> [!proof] Proof sketch
> The algorithm does the following $p$ times:
> 1. forward pass: compute $y_j(\vec w,\vec x_k)$;
> 2. backpropagation: compute $\frac{\partial E_k}{\partial y_j}$;
> 3. compute $\frac{\partial E_k}{\partial w_{ji}}$ and add it to $\mathcal E_{ji}$ (constant time per weight).
>
> Steps 1–3 each take time linear in the number of weights: each weight/edge is used a constant number of times (once forward, once backward, once for its own derivative). So the total is $O(p\cdot|\vec w|)$.
> **Note:** the *speed of convergence* of gradient descent cannot be estimated this way.

## 8. Illustration – XOR (slide 95)

A 2-2-1 network trained on XOR (Duda, Hart, Stork). During training, the hidden representations $(y_1,y_2)$ of the four inputs move so that they become linearly separable by the output neuron ("final decision boundary"). Learning finds the internal representation.

---

## 9. Online algorithm and SGD (slides 96–97)

> [!definition] Online algorithm (slide 96)
> Same as batch, but each step uses only **one** training example $k$:
> $$w_{ji}^{(t+1)} = w_{ji}^{(t)} + \Delta w_{ji}^{(t)},\qquad\Delta w_{ji}^{(t)} = -\varepsilon(t)\cdot\frac{\partial E_k}{\partial w_{ji}}\big(\vec w^{(t)}\big)$$
> where $0<\varepsilon(t)\le 1$. Other variants differ in how the training examples used for the error are selected.

> [!definition] Stochastic gradient descent, SGD (slide 97)
> - Weights in $\vec w^{(0)}$ are randomly initialized to values close to $0$.
> - In step $t+1$: **choose (randomly) a set of training examples** $T\subseteq\{1,\dots,p\}$ (a **minibatch**) and compute
> $$\vec w^{(t+1)} = \vec w^{(t)} + \Delta\vec w^{(t)}, \qquad \Delta\vec w^{(t)} = -\varepsilon(t)\cdot\sum_{k\in T}\nabla E_k\big(\vec w^{(t)}\big)$$
> - $0<\varepsilon(t)\le 1$ is the learning rate in step $t+1$; $\nabla E_k(\vec w^{(t)})$ is the gradient of the error of example $k$.
> - In practice, the random choice of minibatches is implemented by **randomly shuffling all data and then taking minibatches sequentially**.
> - (For CNNs the slides use the averaged version $-\varepsilon(t)\frac{1}{|T|}\sum_{k\in T}\nabla E_k$. One **epoch** = one round through all the data. See [[PV021-11 Convolutional Networks]].)

| Variant | Examples per step | Gradient used |
|---|---|---|
| Batch GD | all $p$ | $\nabla E = \sum_{k=1}^p\nabla E_k$ |
| Online | 1 | $\nabla E_k$ |
| SGD (minibatch) | $\lvert T\rvert$ | $\sum_{k\in T}\nabla E_k$ |

---

## 10. Worked mini-example (checked numerically)

Network: $x\to h\to y$. Hidden neuron $h$ is logistic with $w_{h0}=0$, $w_{hx}=1$. Output neuron is the identity with $w_{y0}=0$, $w_{yh}=2$. One example $x=1$, $d=1$, squared error.

| quantity | value |
|---|---|
| $\xi_h = 0 + 1\cdot 1$ | $1$ |
| $y_h=\sigma(1)$ | $0.7311$ |
| $y = \xi_y = 2\cdot 0.7311$ | $1.4621$ |
| $E=\frac12(y-d)^2$ | $0.1068$ |
| $\partial E/\partial y = y-d$ | $0.4621$ |
| $\partial E/\partial w_{yh} = 0.4621\cdot\underbrace{1}_{\sigma'_y}\cdot 0.7311$ | $0.3378$ |
| $\partial E/\partial y_h = 0.4621\cdot 1\cdot w_{yh}$ | $0.9242$ |
| $\sigma'(\xi_h)=y_h(1-y_h)$ | $0.1966$ |
| $\partial E/\partial w_{hx} = 0.9242\cdot0.1966\cdot 1$ | $0.1817$ |

---

## Rehearsal

> [!question]- What do $j_\leftarrow$, $j^\rightarrow$ and $w_{ji}$ mean?
> $j_\leftarrow$: predecessors of $j$ (arcs into $j$). $j^\rightarrow$: successors of $j$ (arcs out of $j$). $w_{ji}$: the weight of the connection **from $i$ to $j$**.

> [!question]- Write the full gradient formula for an MLP with squared error.
> $\frac{\partial E}{\partial w_{ji}}=\sum_k\frac{\partial E_k}{\partial w_{ji}}$ with $\frac{\partial E_k}{\partial w_{ji}}=\frac{\partial E_k}{\partial y_j}\sigma_j'(\xi_j)y_i$. Here $\frac{\partial E_k}{\partial y_j}=y_j-d_{kj}$ for $j\in Y$, and $\frac{\partial E_k}{\partial y_j}=\sum_{r\in j^\to}\frac{\partial E_k}{\partial y_r}\sigma'_r(\xi_r)w_{rj}$ for hidden $j$.

> [!question]- Why is $\partial\xi_j/\partial w_{ji} = y_i$ and $\partial \xi_r/\partial y_j = w_{rj}$?
> $\xi_j=\sum_{r\in j_\leftarrow}w_{jr}y_r$ is linear in each $w_{jr}$ and in each $y_r$; only the term with $r=i$ (resp. $s = j$) survives the derivative.

> [!question]- Why must backprop go layer by layer from the top?
> $\partial E_k/\partial y_j$ needs $\partial E_k/\partial y_r$ for all successors $r\in j^\to$, which lie in the next layer.

> [!question]- Complexity of computing the batch gradient?
> $O(p\cdot W)$: linear in network size ($W$ weights) times training-set size $p$ (unit-cost operations). Convergence speed is a separate question and cannot be estimated.

> [!question]- Batch vs online vs SGD?
> Batch: the full gradient $\sum_{k=1}^p\nabla E_k$. Online: a single $\nabla E_k$. SGD: a random minibatch $T$, $\sum_{k\in T}\nabla E_k$ (implemented by shuffling and then taking sequential minibatches).
