---
course: PV021 Neural Networks
topic: Neural network = architecture + activity + learning
slides: 25–40
exam: "All definitions formally"
tags: [pv021, definitions, exam]
---

# 02 · Neural Network: Architecture, Activity, Learning (slides 25–40)

Up: [[PV021-00 Index]] · Prev: [[PV021-01 Formal Neuron and Geometry]] · Next: [[PV021-03 Expressive Power and Computability]]

> [!definition] Neural network (slide 25)
> A **neural network** consists of formal neurons interconnected so that the output of one neuron is an input of several other neurons.
> To describe a particular type of network we must specify:
> 1. **Architecture**: how the neurons are connected.
> 2. **Activity**: how the network transforms inputs to outputs.
> 3. **Learning**: how the weights are changed during training.

---

## 1. Architecture (slides 26–28)

> [!definition] Architecture
> The **network architecture** is a **digraph** whose nodes are neurons and whose edges are connections.
> Categories of neurons:
> - **input** neurons
> - **output** neurons
> - **hidden** neurons: those that are neither input nor output
>
> (In general a neuron may be both input and output.)

> [!definition] Cyclic vs acyclic (slide 27)
> - A network is **cyclic (recurrent)** if its architecture contains a directed cycle.
> - Otherwise it is **acyclic (feed-forward)**.

> [!definition] Multilayer perceptron, MLP (slide 28)
> - Neurons are partitioned into **layers**: one input layer, one output layer, possibly several hidden layers.
> - Layers are numbered from 0; the **input layer has number 0**. E.g. a *three-layer network* has two hidden layers and one output layer.
> - Neurons in the $i$-th layer are connected with **all** neurons in the $(i+1)$-st layer.
> - The architecture is described by the numbers of neurons in the layers, e.g. **2-4-3-2**.

---

## 2. Activity (slides 29–37)

Consider a network with $n$ neurons, $k$ input neurons and $\ell$ output neurons.

> [!definition] State, input, initial state (slide 29)
> - **State** of a network: the vector of output values of all neurons. States are vectors of $\mathbb R^n$.
> - **State-space**: the set of all states.
> - **Network input**: a vector of $k$ real numbers, i.e. an element of $\mathbb R^k$.
> - **Network input space**: the set of all network inputs (sometimes a proper subset of $\mathbb R^k$).
> - **Initial state**: the input neurons are set to the values of the network input (each component corresponds to one input neuron); all remaining neurons are set to $0$.

> [!definition] Computation (slide 30)
> Computation (typically) proceeds in **discrete steps**. In every step:
> 1. a set of neurons is **selected** according to some rule;
> 2. the selected neurons change their states according to their inputs (they are simply evaluated). A neuron without inputs keeps its value constant.
>
> A computation is **finite** on a network input $\vec x$ if the state changes only finitely many times, i.e. there is a moment after which the state never changes. We then say the network **stops on $\vec x$**.
>
> **Network output**: the vector of values of all output neurons, an element of $\mathbb R^\ell$. It keeps changing during the computation!
>
> **MLP selection rule:** in the $i$-th step evaluate all neurons in the $i$-th layer.

> [!definition] Semantics of a network (slide 31)
> Let $A\subseteq\mathbb R^k$ and $B\subseteq \mathbb R^\ell$, and suppose the network stops on every input from $A$.
> The network **computes a function $F: A\to B$** if for every network input $\vec x$, the vector $F(\vec x)\in B$ is the output of the network after the computation on $\vec x$ stops.
> *Example:* a 2-2-1 network computes a function from $\mathbb R^2$ to $\mathbb R$.

### Inner potentials and activation functions (slides 32–34)

To specify the activity we specify how each $\xi$ is computed and what each $\sigma$ is.

**Default inner potential** ($\vec x$ = the neuron's inputs, $\vec w$ = its weights):
$$\xi = w_0 + \sum_{i=1}^n w_i\cdot x_i$$
**Special networks** may use a "distance" of the input from the weight vector instead:
$$\xi = \|\vec x - \vec w\|$$
where $\|\cdot\|$ is a vector norm, typically Euclidean.

| Name | Formula | Notes |
|---|---|---|
| Unit step | $\sigma(\xi) = 1$ if $\xi\ge 0$, $0$ if $\xi<0$ | not differentiable |
| (Logistic) sigmoid | $\sigma(\xi) = \dfrac{1}{1+e^{-\lambda\xi}}$ | $\lambda\in\mathbb R$ is the **steepness** |
| Hyperbolic tangent (as on slide) | $\sigma(\xi) = \dfrac{1-e^{-\xi}}{1+e^{-\xi}}$ | range $(-1,1)$ |
| ReLU | $\sigma(\xi) = \max(\xi,0)$ | |
| Leaky ReLU (slide 34) | $\max(0.1x, x)$ | |
| Maxout (slide 34) | $\max(w_1^\top x + b_1,\; w_2^\top x + b_2)$ | |
| ELU (slide 34) | $x$ for $x\ge 0$; $\alpha(e^x-1)$ for $x<0$ | |

> [!warning] Detail on "tanh" (checked)
> The slide's formula $\frac{1-e^{-\xi}}{1+e^{-\xi}}$ equals $\tanh(\xi/2) = 2\,\sigma_{\text{logistic}}(\xi)-1$.
> The standard $\tanh(\xi)=\frac{1-e^{-2\xi}}{1+e^{-2\xi}}$. Both have the same shape (range $(-1,1)$) and differ only in steepness. Write whichever your lecturer used, and know they differ by a factor of 2 inside.

### XOR network (slide 35) — computation of an MLP

Unit step activation everywhere. Weights (bias = weight from constant input 1):

| Neuron | bias $w_0$ | weight from $x_1$ | weight from $x_2$ | computes |
|---|---|---|---|---|
| hidden 1 | $-1$ | $2$ | $2$ | $\mathrm{OR}(x_1,x_2)$ |
| hidden 2 | $3$ | $-2$ | $-2$ | $\mathrm{NAND}(x_1,x_2)$ |
| output | $-2$ | $1$ (from $h_1$) | $1$ (from $h_2$) | $\mathrm{AND}(h_1,h_2)$ |

So $y = \mathrm{AND}(\mathrm{OR}, \mathrm{NAND}) = \mathrm{XOR}$. The table is verified numerically:

| $x_1$ | $x_2$ | $h_1=\sigma(-1+2x_1+2x_2)$ | $h_2=\sigma(3-2x_1-2x_2)$ | $y=\sigma(-2+h_1+h_2)$ |
|---|---|---|---|---|
| 1 | 1 | $\sigma(3)=1$ | $\sigma(-1)=0$ | $\sigma(-1)=0$ |
| 1 | 0 | $\sigma(1)=1$ | $\sigma(1)=1$ | $\sigma(0)=1$ |
| 0 | 1 | $1$ | $1$ | $1$ |
| 0 | 0 | $\sigma(-1)=0$ | $\sigma(3)=1$ | $\sigma(-1)=0$ |

### MLP and linear separation (slide 36)

Each hidden neuron is one line in the input plane:
- $P_1:\; -1 + 2x_1 + 2x_2 = 0$
- $P_2:\; 3 - 2x_1 - 2x_2 = 0$

The output neuron fires iff the input lies **between** the two parallel lines (on the positive side of both), which is exactly where $(0,1)$ and $(1,0)$ lie.

> [!tip] Lesson
> Hidden neurons map the input into a new representation, here $(h_1,h_2)$, in which the problem becomes linearly separable.

### Example of a cyclic network (slide 37)

One input $x_1 = 1$, unit step activations, three neurons A, B, C. As read from the figure:
- $\xi_A = -2 + 2x_1 - 5y_B$
- $\xi_B = -1 + y_C$
- $\xi_C = -2 + x_1 + y_A + y_B$

Initial state $(A,B,C) = (0,0,0)$. One neuron is evaluated per step:

| step | evaluated | $(y_A, y_B, y_C)$ |
|---|---|---|
| 0 | – | (0, 0, 0) |
| 1 | A: $-2+2-0 = 0 \ge 0$ | (1, 0, 0) |
| 2 | C: $-2+1+1+0 = 0$ | (1, 0, 1) |
| 3 | B: $-1+1 = 0$ | (1, 1, 1) |
| 4 | A: $-2+2-5 = -5$ | (0, 1, 1) |

The final state $(0,1,1)$ is **stable**: re-evaluating any neuron does not change it, so the network **stops** with output values $A=0$, $B=1$.

> [!tip] Point of the example
> In cyclic networks the result depends on the **selection rule** (order of evaluation), and termination is not guaranteed in general.

---

## 3. Learning (slides 38–39)

Consider a network with $n$ neurons, $k$ input and $\ell$ output neurons.

> [!definition] Configuration and weight space (slide 38)
> - **Configuration** of a network: the vector of all values of weights. For a network with $m$ connections, configurations are elements of $\mathbb R^m$.
> - **Weight-space**: the set of all configurations.
> - **Initial configuration**: weights initialized randomly or by some sophisticated algorithm (see [[PV021-09 Activations, Preprocessing, Initialization]]).

> [!definition] Learning algorithms (slide 39)
> **Learning rule**: a rule for weight adaptation. The goal is to find a configuration in which the network computes a desired function.
> - **Supervised learning**: the desired function is described by **training examples**, pairs (input, output). The algorithm searches for a configuration that "corresponds" to the examples, typically by **minimizing an error function**.
> - **Unsupervised learning**: the training set contains only inputs. The goal is to determine the distribution of the inputs (clustering, deep belief networks, …).

## 4. Advantages of neural networks (slide 40)

- **Massive parallelism**: neurons can be evaluated in parallel.
- **Learning**: many sophisticated learning algorithms "program" the network.
- **Generalization and robustness**: information is encoded in a distributed way in the weights; "close" inputs typically get similar values.
- **Graceful degradation**: damage typically causes only a decrease in the precision of results.

---

## Rehearsal

> [!question]- What three things define a type of neural network?
> Architecture (how neurons are connected), activity (how inputs are transformed to outputs), learning (how weights change during training).

> [!question]- Define state, state-space, network input, initial state.
> State: the vector of outputs of all $n$ neurons, $\in\mathbb R^n$. State-space: the set of all states. Network input: a vector in $\mathbb R^k$ ($k$ input neurons). Initial state: input neurons take the input values, all others are 0.

> [!question]- When does a network "stop on $\vec x$"? When does it compute $F:A\to B$?
> It stops on $\vec x$ when the state changes only finitely many times. It computes $F$ if it stops on every $\vec x\in A$ and the output after stopping is $F(\vec x)\in B$.

> [!question]- What is the MLP selection rule?
> In the $i$-th step evaluate all neurons of the $i$-th layer.

> [!question]- Configuration vs state?
> Configuration = vector of all weights ($\in\mathbb R^m$ for $m$ connections). State = vector of all neuron outputs ($\in \mathbb R^n$).

> [!question]- Give XOR weights for a 2-2-1 step network.
> $h_1=\sigma(-1+2x_1+2x_2)$ (OR), $h_2=\sigma(3-2x_1-2x_2)$ (NAND), $y=\sigma(-2+h_1+h_2)$ (AND).
