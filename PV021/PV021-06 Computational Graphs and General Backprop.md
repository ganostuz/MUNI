---
course: PV021 Neural Networks
topic: Computational graphs, Jacobians, general backpropagation
slides: 103–110
exam: "Everything in great detail, including the general backpropagation from slide 109"
tags: [pv021, backprop, jacobian, computational-graph, exam]
---

# 06 · Backprop in Computational Graphs (slides 103–110)

Up: [[PV021-00 Index]] · Prev: [[PV021-05 Output Activations and Error Functions]] · Next: [[PV021-07 Gradient Descent Issues and Minibatches]]

---

## 1. Computational graph (slide 104)

> [!definition] Computational graph
> A **directed acyclic graph** $(V,E)$ whose nodes are denoted $n,m,\dots$
> - $n_\leftarrow$ = the set of all nodes $m$ such that $(m,n)\in E$ (predecessors).
> - $n^\rightarrow$ = the set of all nodes $m$ such that $(n,m)\in E$ (successors).
> - **Input nodes**: $n_\leftarrow=\emptyset$. **Output nodes**: $n^\rightarrow = \emptyset$. **Hidden nodes**: the rest.
> - Each node $n$ has an **output value** $\mathbf y^n$, which is a **vector**.
> - Each non-input node $n$ is assigned a **function** $f^n$ such that
> $$\mathbf y^n = f^n(\mathbf y^{n_1},\dots,\mathbf y^{n_k})\quad\text{where } n_\leftarrow = \{n_1,\dots,n_k\}$$
>   (assuming a fixed ordering of $n_\leftarrow$).

## 2. Semantics (slide 105)

> [!definition] Computation
> **Input:** a collection of vectors $\mathbf v^n$, one for each input node $n$.
> **Computation:**
> - Start with $\mathbf y^n = \mathbf v^n$ for every input node $n$.
> - While some node has not been evaluated: select a node $n$ **all of whose predecessors have been evaluated** and compute $\mathbf y^n = f^n(\mathbf y^{n_1},\dots,\mathbf y^{n_k})$.
>
> **Output:** the collection of values of all output nodes (a collection of vectors).
> (This is evaluation in a **topological order**. It always terminates because the graph is acyclic.)

## 3. MLP as a computational graph (slide 106)

> [!example] $k$-layer MLP
> - $V = \{0,1,\dots,k\}$, $E = \{(n-1,n)\mid n = 1,\dots,k\}$ (a chain).
> - Each node $n=1,\dots,k$ gets the function
> $$\mathbf y^n = f^n(\mathbf y^{n-1}) = \sigma^n(W^n\mathbf y^{n-1})$$
>   where $W^n$ is a **weight matrix** and $\sigma^n=(\sigma^n_1,\dots,\sigma^n_r)$ is a vector of activation functions applied component-wise: $\sigma^n(x_1,\dots,x_r) = (\sigma_1^n(x_1),\dots,\sigma_r^n(x_r))$.
> - Input node $0$ gets the input vector; output node $k$ gives the output vector.
> - (A bias can be included by appending a constant 1 to $\mathbf y^{n-1}$.)

## 4. Differentiation: the Jacobian (slide 107)

> [!definition] Jacobian of node $n$ w.r.t. node $m$, for $(m,n)\in E$
> $$\frac{\partial\mathbf y^n}{\partial\mathbf y^m} = \begin{pmatrix}\frac{\partial\mathbf y_1^n}{\partial\mathbf y_1^m}&\frac{\partial\mathbf y_1^n}{\partial\mathbf y_2^m}&\cdots&\frac{\partial\mathbf y_1^n}{\partial\mathbf y_J^m}\\ \frac{\partial\mathbf y_2^n}{\partial\mathbf y_1^m}&\frac{\partial\mathbf y_2^n}{\partial\mathbf y_2^m}&\cdots&\frac{\partial\mathbf y_2^n}{\partial\mathbf y_J^m}\\ \vdots&\vdots&\ddots&\vdots\\ \frac{\partial\mathbf y_I^n}{\partial\mathbf y_1^m}&\frac{\partial\mathbf y_I^n}{\partial\mathbf y_2^m}&\cdots&\frac{\partial\mathbf y_I^n}{\partial\mathbf y_J^m}\end{pmatrix}$$
> It is obtained by differentiating each component of $\mathbf y^n$ w.r.t. each component of $\mathbf y^m$. Its $ij$ element is
> $$\frac{\partial\mathbf y^n_i}{\partial\mathbf y^m_j} = \frac{\partial\big(f^n(\mathbf y^{n_1},\dots,\mathbf y^{n_k})\big)_i}{\partial\mathbf y_j^m}$$
> i.e. the value $\mathbf y^n_i$ differentiated w.r.t. $\mathbf y^m_j$. Rows = components of the **output** $\mathbf y^n$ (dimension $I$); columns = components of the **input** $\mathbf y^m$ (dimension $J$).

## 5. Jacobian of an MLP layer (slide 108)

> [!important] MLP layer Jacobian
> For $\mathbf y^n = \sigma^n(W^n\mathbf y^{n-1})$:
> $$\frac{\partial\mathbf y^n}{\partial\mathbf y^{n-1}} = \dot\sigma^n\,W^n,\qquad \dot\sigma^n = \begin{pmatrix}\dot\sigma^n_1&0&\cdots&0\\0&\dot\sigma^n_2&\cdots&0\\\vdots&\vdots&\ddots&\vdots\\0&0&\cdots&\dot\sigma^n_r\end{pmatrix}$$
> $\dot\sigma^n$ is the **diagonal matrix of derivatives** of the components of $\sigma^n$, evaluated at the potentials $W^n\mathbf y^{n-1}$.

> [!proof] Proof
> $\mathbf y^n_i = \sigma^n_i\big(\sum_j W^n_{ij}\mathbf y^{n-1}_j\big)$. By the chain rule
> $$\frac{\partial\mathbf y^n_i}{\partial\mathbf y_j^{n-1}} = \dot\sigma^n_i\cdot W^n_{ij} = (\dot\sigma^nW^n)_{ij}$$
> where $\dot\sigma^n_i = (\sigma^n_i)'\big((W^n\mathbf y^{n-1})_i\big)$. Multiplying by a diagonal matrix on the left scales row $i$ by $\dot\sigma^n_i$. ∎ (Checked numerically against finite differences.)

---

## 6. General backpropagation (slide 109): the key slide

> [!important] Backpropagation in a computational graph
> Fix a node $o$. Our aim is to compute
> $$\frac{\partial\mathbf y^o}{\partial\mathbf y^n}\quad\text{for all predecessors } n \text{ of } o \text{ (all nodes from which } o \text{ is reachable).}$$
> **Base:**
> $$\frac{\partial\mathbf y^o}{\partial\mathbf y^o} = I \quad(\text{identity matrix})$$
> **Step** (by an appropriately general version of the chain rule):
> $$\boxed{\frac{\partial\mathbf y^o}{\partial\mathbf y^n} = \sum_{m\in n^\rightarrow}\frac{\partial\mathbf y^o}{\partial\mathbf y^m}\,\frac{\partial\mathbf y^m}{\partial\mathbf y^n}}$$
> Now **propagate the gradient from $o$ to all its predecessors $n$**, i.e. process nodes in **reverse topological order**.

> [!proof] Why the formula holds
> - $\mathbf y^n$ influences $\mathbf y^o$ **only through its successors** $m\in n^\rightarrow$. Every directed path from $n$ to $o$ starts with an edge $(n,m)$, $m\in n^\rightarrow$.
> - View $\mathbf y^o$ as a function of the successor values $\mathbf y^{m_1},\dots,\mathbf y^{m_s}$, each of which is a function of $\mathbf y^n$. The **multivariate chain rule** sums the contributions along all successors. In matrix form, the Jacobian of a composition is the product of Jacobians: $\frac{\partial \mathbf y^o}{\partial \mathbf y^m}\frac{\partial\mathbf y^m}{\partial \mathbf y^n}$.
> - If $m$ cannot reach $o$, then $\frac{\partial\mathbf y^o}{\partial\mathbf y^m}=0$.
> - **Order:** when we process $n$, all its successors $m$ must already be done. Reverse topological order guarantees this because the graph is a DAG.
> - **Cost:** each edge $(n,m)$ contributes one product, so the cost is linear in the number of edges (times the cost of the Jacobian products).

> [!tip] Dimensions
> If $\mathbf y^o$ is a scalar (the error), then $\frac{\partial\mathbf y^o}{\partial\mathbf y^n}$ is a $1\times\dim(\mathbf y^n)$ **row vector**, and backprop is a sequence of row-vector × Jacobian products (a "vector–Jacobian product"). This is why reverse-mode differentiation is cheap for a scalar loss.

## 7. MLP with an error node (slide 110)

> [!example] MLP + error node
> - $V = \{0,1,\dots,k,o\}$, $E = \{(n-1,n)\mid n=1,\dots,k\}\cup\{(k,o)\}$.
> - Each $n=1,\dots,k$: $\mathbf y^n = f^n(\mathbf y^{n-1}) = \sigma^n(W^n\mathbf y^{n-1})$.
> - $f^o$ is the **error function** (e.g. squared error).
>
> **Backprop:** $\frac{\partial\mathbf y^o}{\partial\mathbf y^k}$ is the derivative of the error function. For $n\le k$:
> $$\boxed{\frac{\partial\mathbf y^o}{\partial\mathbf y^{n-1}} = \frac{\partial\mathbf y^o}{\partial\mathbf y^n}\frac{\partial\mathbf y^n}{\partial\mathbf y^{n-1}} = \frac{\partial\mathbf y^o}{\partial\mathbf y^n}\,\dot\sigma^n W^n}$$
> (Each node has exactly one successor here, so the sum has one term.)

> [!note] Getting the weight gradients (completes the picture; checked numerically)
> Let $\mathbf g^n = \frac{\partial\mathbf y^o}{\partial\mathbf y^n}$ (a row vector). Since $\mathbf y^n_i=\sigma^n_i(\sum_jW^n_{ij}\mathbf y^{n-1}_j)$:
> $$\frac{\partial\mathbf y^o}{\partial W^n_{ij}} = \mathbf g^n_i\,\dot\sigma^n_i\,\mathbf y^{n-1}_j\qquad\Longleftrightarrow\qquad \frac{\partial\mathbf y^o}{\partial W^n} = \big(\dot\sigma^n(\mathbf g^n)^\top\big)\,(\mathbf y^{n-1})^\top\quad(\text{an outer product})$$
> This is exactly slide 88's $\frac{\partial E_k}{\partial w_{ji}} = \frac{\partial E_k}{\partial y_j}\sigma_j'(\xi_j)y_i$ in matrix form, and the recursion above is the matrix form of $\frac{\partial E_k}{\partial y_j}=\sum_r\frac{\partial E_k}{\partial y_r}\sigma'_r(\xi_r)w_{rj}$.

> [!example] Square-error case
> If $\mathbf y^o = \frac12\|\mathbf y^k - \mathbf d\|^2$, then $\frac{\partial\mathbf y^o}{\partial\mathbf y^k} = (\mathbf y^k - \mathbf d)^\top$ and
> $$\frac{\partial\mathbf y^o}{\partial\mathbf y^{k-1}} = (\mathbf y^k-\mathbf d)^\top\dot\sigma^kW^k,\quad\frac{\partial\mathbf y^o}{\partial\mathbf y^{k-2}} = (\mathbf y^k-\mathbf d)^\top\dot\sigma^kW^k\dot\sigma^{k-1}W^{k-1},\ \dots$$
> A **product of Jacobians** like this is the source of vanishing/exploding gradients ([[PV021-07 Gradient Descent Issues and Minibatches]], [[PV021-13 Recurrent Networks]]).

---

## Rehearsal

> [!question]- Define a computational graph and its semantics.
> A DAG where each node $n$ carries a vector $\mathbf y^n$ and each non-input node has a function $f^n$ with $\mathbf y^n=f^n(\mathbf y^{n_1},\dots,\mathbf y^{n_k})$ over its predecessors. Inputs are set to $\mathbf v^n$; nodes are evaluated once all their predecessors are evaluated; the output is the values of the output nodes.

> [!question]- State the general backprop rule.
> $\frac{\partial\mathbf y^o}{\partial\mathbf y^o}=I$ and $\frac{\partial\mathbf y^o}{\partial\mathbf y^n}=\sum_{m\in n^\to}\frac{\partial\mathbf y^o}{\partial\mathbf y^m}\frac{\partial\mathbf y^m}{\partial\mathbf y^n}$, computed in reverse topological order.

> [!question]- Jacobian of an MLP layer?
> $\frac{\partial\mathbf y^n}{\partial\mathbf y^{n-1}}=\dot\sigma^nW^n$, where $\dot\sigma^n=\operatorname{diag}((\sigma^n_i)'((W^n\mathbf y^{n-1})_i))$.

> [!question]- What is $\partial\mathbf y^o/\partial\mathbf y^{n-1}$ in an MLP with error node?
> $\frac{\partial\mathbf y^o}{\partial\mathbf y^n}\dot\sigma^nW^n$.

> [!question]- How do you get $\partial E/\partial W^n$ from $\mathbf g^n=\partial E/\partial \mathbf y^n$?
> $\partial E/\partial W^n_{ij}=\mathbf g^n_i\dot\sigma^n_i\mathbf y^{n-1}_j$, i.e. the outer product $(\dot\sigma^n\mathbf g^{n\top})(\mathbf y^{n-1})^\top$.
