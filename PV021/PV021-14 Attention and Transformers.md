---
course: PV021 Neural Networks
topic: Attention, self-attention, masked & multi-head attention, positional encoding, layer norm, GPT; self-attention backprop
slides: 275–295
exam: "All mathematical details and intuition. Know how the self-attention layer works. May be asked to DERIVE backprop for self-attention (possibly with α_ij = e_ij)"
tags: [pv021, attention, transformer, gpt, backprop, proofs, exam]
---

# 14 · Attention and Transformers (slides 275–295)

Up: [[PV021-00 Index]] · Prev: [[PV021-13 Recurrent Networks]] · Next: [[PV021-15 Exam Drill]]

---

## 1. Motivation (slides 275–277)

> [!note] Sequence-to-sequence task (slide 275)
> Given a sequence of vectors $\mathbf x = \vec x_1,\dots,\vec x_T$, generate a new sequence $\mathbf y = \vec y_1,\dots,\vec y_T$ (for now of the same length).
> Simply use a recurrent network, possibly **augmented with attention** (whiteboard).

> [!note]- RNN + attention (the whiteboard part; standard formulation, not in the handout)
> An encoder RNN produces hidden states $\vec h_1,\dots,\vec h_T$. To produce output $i$, compute relevance scores $e_{ij}=\operatorname{score}(\vec s_{i},\vec h_j)$ between the current decoder state $\vec s_i$ and every $\vec h_j$. Normalize them with softmax, $\alpha_{ij}=\frac{\exp e_{ij}}{\sum_k\exp e_{ik}}$, and use the **context vector** $\vec c_i=\sum_j\alpha_{ij}\vec h_j$ as additional input. The decoder can then "look at" any position directly instead of squeezing everything into one memory vector.

> [!question] Do we still need the recurrence? (slide 276)
> - The attention mechanism extracts information from the sequence quite well.
> - Is there a reason for reading the input sequentially?
> - Could we remove the recurrent network itself and keep only the attention? → **"Attention Is All You Need"** (Vaswani et al., 2017, slide 277): the Transformer.

---

## 2. Self-attention layer (slide 278) ★

> [!definition] Self-attention
> Fix an input sequence $\vec x_1,\dots,\vec x_T$. Consider three **learnable matrices** $W_q, W_k, W_v$ and generate sequences of **queries, keys and values**:
> - $\vec q_1,\dots,\vec q_T$ where $\vec q_i = W_q\vec x_i$ for all $i=1,\dots,T$
> - $\vec k_1,\dots,\vec k_T$ where $\vec k_i = W_k\vec x_i$
> - $\vec v_1,\dots,\vec v_T$ where $\vec v_i = W_v\vec x_i$
>
> Define a **score** for all $i,j\in\{1,\dots,T\}$:
> $$e_{ij} = \vec q_i\cdot\vec k_j$$
> Intuitively, $e_{ij}$ measures how much the input at position $i$ is related to the input at position $j$, i.e. how well the query fits the key.
>
> Define the **attention weights**
> $$\alpha_{ij} = \frac{\exp\big(e_{ij}/\sqrt{d_{attn}}\big)}{\sum_{k=1}^T\exp\big(e_{ik}/\sqrt{d_{attn}}\big)}$$
> i.e. apply the good old **softmax** to $(e_{i1},\dots,e_{iT})/\sqrt{d_{attn}}$. Here $d_{attn}$ is the dimension of the vectors (slide: "of $\vec v_i$"; in the original paper it is the query/key dimension $d_k$; usually they are equal).
>
> Define the **outputs**:
> $$\vec y_i = \sum_{j=1}^T\alpha_{ij}\cdot\vec v_j$$

> [!tip] Intuition and properties
> - Each output $\vec y_i$ is a **convex combination of the values** ($\alpha_{ij}\ge0$, $\sum_j\alpha_{ij}=1$), weighted by how well query $i$ matches each key $j$. It is a soft, differentiable dictionary lookup.
> - **Every position sees every position in one step**: path length $O(1)$, unlike $O(T)$ in an RNN, so there is no long-term-dependency problem. Everything is computed in parallel. The cost is $O(T^2)$ scores.
> - **Why $\sqrt{d_{attn}}$:** if the components of $\vec q,\vec k$ are independent with mean $0$ and variance $1$, then $\vec q\cdot\vec k = \sum_{c=1}^{d}q_ck_c$ has variance $d$ (the same computation as the LeCun derivation in [[PV021-09 Activations, Preprocessing, Initialization]]). Dividing by $\sqrt d$ restores variance 1 and keeps the softmax out of saturation, where gradients would be tiny.
> - Permuting the inputs just permutes the outputs (permutation-equivariant), so the layer **does not know positions** → positional encoding (section 7).
> - **Matrix form:** with $X\in\mathbb R^{T\times d_x}$ (rows $\vec x_i^\top$): $Q = XW_q^\top$, $K = XW_k^\top$, $V = XW_v^\top$, $A = \operatorname{softmax}_{\text{rows}}\!\big(QK^\top/\sqrt{d_{attn}}\big)$, $Y = AV$.

## 3. Explainability and self-attention (slides 279–281)

- **BertViz:** visualizes $\alpha_{ij}$; the intensity of the lines corresponds to $\alpha_{ij}$.
- **Bias detection** (slide 280): the visualization can reveal model bias. Here, according to the model, *He ≈ Doctor* and *She ≈ Nurse*.
- **Neuron view** (slide 281): shows the query $\vec q$, the key $\vec k$, their element-wise product, $\vec q\cdot\vec k$ and the softmax for a chosen token.

---

## 4. Language model (slide 282)

> [!definition] Language model
> A sequence of **tokens** $a_1,\dots,a_T\in\Sigma^*$ (e.g. words from a vocabulary $\Sigma$).
> **The goal:** maximize
> $$\prod_{k=1}^TP(a_k\mid a_1,\dots,a_{k-1};W)\quad\big(=P(a_1,\dots,a_T;W)\big)$$
> where $P$ is the conditional probability measure over $\Sigma$ modelled by a neural network with weights $W$. (The equality is the chain rule of probability.)
> **Generation:** given $a_1,\dots,a_k$, sample $a_{k+1}$ from $P(a_{k+1}\mid a_1,\dots,a_k;W)$.

## 5. GPT architecture (slides 283–284, 288, 292)

```
          ┌ Text Prediction │ Task Classifier ┐
          └────────────────┬────────────────┘
       ┌───────────────────┴──────────────────┐
       │  Layer Norm                          │
       │      ⊕ ◀── residual ──┐              │
       │  Feed Forward         │              │   × 12
       │  Layer Norm           │              │
       │      ⊕ ◀── residual ──┐              │
       │  Masked Multi Self Attention         │
       └───────────────────┬──────────────────┘
                 Text & Position Embed
```
- GPT (GPT-1): **12 blocks**. Each block: **masked multi-head self-attention** → add residual → **layer norm** → **feed forward** (position-wise MLP) → add residual → **layer norm**.
- The input is the **text (token) embedding + positional encoding** (slide 288 arrow).
- **GPT-2** (slide 292): **layer normalization is moved to the input of each sub-block** ("pre-LN"), and an additional layer norm is added after the final block.

## 6. Masked self-attention (slide 285)

> [!definition] Masked self-attention
> Assume an attention mechanism that generates $\vec y_1,\dots,\vec y_T$ from $\vec x_1,\dots,\vec x_T$.
> **The problem:** how to generate $\vec y_k$ based only on $\vec x_1,\dots,\vec x_{k-1}$ (no peeking into the future)?
> Define the score
> $$e_{ij} = \begin{cases}\vec q_i\cdot\vec k_j & \text{if } j<i\\ -\infty&\text{otherwise}\end{cases}$$
> so that
> $$\alpha_{ij} = \begin{cases}\dfrac{\exp(e_{ij}/\sqrt{d_{attn}})}{\sum_{k=1}^T\exp(e_{ik}/\sqrt{d_{attn}})}&\text{if } j<i\\[4pt] 0 & \text{otherwise}\end{cases}$$
> (because $\exp(-\infty)=0$, the masked positions contribute nothing to the denominator), and
> $$\vec y_i = \sum_{j=1}^T\alpha_{ij}\cdot\vec v_j .$$

> [!warning] $j<i$ vs $j\le i$
> The slide masks $j\ge i$ (strictly the past), matching its goal "$\vec y_k$ from $\vec x_1..\vec x_{k-1}$". Then row $i=1$ has no allowed entries and needs special handling, e.g. a start token. In GPT as actually implemented (and in "GPT – learning" below, where $\vec y_\ell$ predicts $a_{\ell+1}$), the mask allows $j\le i$: position $i$ sees itself and the past and predicts the **next** token. Either way: **masked entries get $-\infty$ before the softmax, so their $\alpha=0$.**

## 7. Multi-head self-attention (slides 286–287)

> [!definition] Multi-head attention
> Let the number of **heads** be $H$. For $h=1,\dots,H$, the $h$-th head is an attention mechanism that, given $\vec x_1,\dots,\vec x_T$, produces $\vec y_1^h,\dots,\vec y_T^h$. The heads' outputs differ: each head has its own matrices $W_q^h,W_k^h,W_v^h$.
> Assume all $\vec y^h_k$ have the same dimension $d_{mid}$, and consider a learnable matrix $W_{out}$ of dimensions $d_{out}\times(H\cdot d_{mid})$. The multi-head attention produces $\vec y_1,\dots,\vec y_T$ where
> $$\vec y_k = W_{out}\cdot\big(\vec y^1_k\odot\vec y^2_k\odot\cdots\odot\vec y^H_k\big)$$
> and $\odot$ here denotes **concatenation** of vectors (not the element-wise product!).

> [!summary] Summary (slide 287)
> - **Input:** a sequence $\vec x_1,\dots,\vec x_T$. **Output:** a sequence $\vec y_1,\dots,\vec y_T$ of the same length; the dimensions of $\vec y_k$ and $\vec x_k$ need not be equal.
> - **Attention:** learnable $W_q,W_k,W_v$, used to compute queries, keys and values from $\vec x_1..\vec x_T$. The outputs are values "scaled" by the query–key attention.
> - **Multi-head attention:** learnable $W^h_q,W^h_k,W^h_v$ for $h=1..H$ (each head operates **independently** on the input) and $W_{out}$ (**linearly transforms the concatenated** results of the heads).
> - Intuition: different heads can attend to different kinds of relations (syntax, coreference, position, …) at the same time.

## 8. Positional encoding (slides 289–291)

> [!definition] Sinusoidal positional encoding
> **Goal:** encode a position (index) $k\in\{1,\dots,T\}$ into a vector $\vec P_k\in\mathbb R^d$.
> For position $k$ and $i\in\{0,\dots,d/2-1\}$:
> $$P_{k,2i} = \sin\Big(\frac{k}{n^{2i/d}}\Big),\qquad P_{k,2i+1} = \cos\Big(\frac{k}{n^{2i/d}}\Big)$$
> Here $n=10000$, a user-defined constant suggested by the original paper.
> (The slide writes $i\in\{0,\dots,d/2\}$; the valid range is up to $d/2-1$, otherwise the index $2i+1$ exceeds $d-1$.)
> Given the input sequence, add the encoding: $\vec x'_k = \vec x_k + \vec P_k$.

> [!example] Slide 290: $d=4$, $n=100$ (all values re-computed ✔)
> For $i=0$ the frequency is $1/100^0 = 1$; for $i=1$ it is $1/100^{2/4} = 1/10$.
> | token | $k$ | $P_{k0}=\sin(k)$ | $P_{k1}=\cos(k)$ | $P_{k2}=\sin(k/10)$ | $P_{k3}=\cos(k/10)$ |
> |---|---|---|---|---|---|
> | I | 0 | 0 | 1 | 0 | 1 |
> | am | 1 | 0.84 | 0.54 | 0.10 | 1.00 |
> | a | 2 | 0.91 | −0.42 | 0.20 | 0.98 |
> | Robot | 3 | 0.14 | −0.99 | 0.30 | 0.96 |

> [!note] Properties (slide 291)
> - Along the position axis: sinusoidal functions. Across the dimensions: **decreasing frequency** (wavelengths from $2\pi$ to $2\pi n$).
> - **For any offset $o\in\{1,\dots,T\}$ there is a linear transformation $M$ such that $M\vec P_k = \vec P_{k+o}$ for every $k\in\{1,\dots,T-o\}$.** Intuitively: just rotate each component pair of $\vec P_k$ appropriately.

> [!proof]- Proof of the linear-shift property (checked numerically)
> Fix a pair $(2i,2i+1)$ with frequency $\omega_i = n^{-2i/d}$. By the angle-addition formulas,
> $$\begin{pmatrix}\sin(\omega_i(k+o))\\\cos(\omega_i(k+o))\end{pmatrix} = \begin{pmatrix}\cos\omega_io & \sin\omega_io\\ -\sin\omega_io&\cos\omega_io\end{pmatrix}\begin{pmatrix}\sin\omega_ik\\\cos\omega_ik\end{pmatrix}$$
> since $\sin(a+b)=\sin a\cos b+\cos a\sin b$ and $\cos(a+b)=\cos a\cos b-\sin a\sin b$. Let $M$ be the block-diagonal matrix of these $2\times2$ rotations. It depends only on $o$, not on $k$. ∎
> Consequence: relative positions are easy for linear maps (such as $W_q, W_k$) to pick up.

## 9. Layer normalization (slide 293)

> [!definition] Layer normalization
> Given a vector $\vec x\in\mathbb R^d$, layer normalization computes
> $$\vec x' = \gamma\cdot\frac{(\vec x-\mu)}{\sigma} + \beta,\qquad \mu = \frac1d\sum_{i=1}^dx_i,\qquad\sigma^2 = \frac1d\sum_{i=1}^d(x_i-\mu)^2$$
> where $\gamma,\beta\in\mathbb R^d$ are vectors of **trainable parameters** (component-wise product).
> **In a transformer**, the input to layer normalization is a sequence $\vec x_1,\dots,\vec x_T$; layer normalization is applied **to each $\vec x_k$ separately**, producing a sequence of "normalized" vectors. (Contrast with batch norm in [[PV021-10 Normalization and Regularization]], which normalizes over the batch.)

## 10. GPT learning (slide 294)

> [!definition] Training GPT
> - A sequence of tokens $a_1,\dots,a_T\in\Sigma$ and their one-hot encodings $\vec u_1,\dots,\vec u_T\in\{0,1\}^{|\Sigma|}$. Assume $a_1$ is a special token marking the start of the sequence.
> - Embed into vectors and add the positional encoding ($W_e$ is an embedding matrix):
> $$\vec x_k = W_e\cdot\vec u_k + \vec P_k\in\mathbb R^d$$
> - Apply the network (the transformer block repeated 12×) to $\vec x_1,\dots,\vec x_T$ and obtain $\vec y_1,\dots,\vec y_T$, where each $\vec y_k\in[0,1]^{\Sigma}$ is a probability distribution on $\Sigma$ (softmax output).
> - **Error** (cross-entropy / negative log-likelihood of the next token):
> $$-\sum_{\ell=1}^{T-1}\log\big(\vec y_\ell[a_{\ell+1}]\big)$$
>   where $\vec y_\ell[a_{\ell+1}]$ is the probability of $a_{\ell+1}$ in the distribution $\vec y_\ell$.
> - Minimizing it maximizes $\prod_\ell P(a_{\ell+1}\mid a_1..a_\ell)$, the language-model goal. The masking makes all $T-1$ predictions trainable **in parallel** from one sequence.

## 11. GPT inference (slide 295)

> [!algorithm] Generating text
> - Tokens $a_1,\dots,a_\ell\in\Sigma$ with one-hot encodings $\vec u_1,\dots,\vec u_\ell$.
> - Embed and add the positional encoding: $\vec x_k = W_e\cdot\vec u_k + \vec P_k$.
> - Apply the network to $\vec x_1,\dots,\vec x_\ell$, obtaining $\vec y_1,\dots,\vec y_\ell$ (each a probability distribution on $\Sigma$).
> - **Sample the next token** $a_{\ell+1}\sim\vec y_\ell$, append it, and repeat.

---

## 12. ★ Backpropagation through a self-attention layer (derive this)

**Setting** (one head; $\delta_i := \frac{\partial E}{\partial\vec y_i}$ is given from the layer above):
$$\vec q_i = W_q\vec x_i,\quad\vec k_j = W_k\vec x_j,\quad\vec v_j = W_v\vec x_j,\quad e_{ij} = \vec q_i\cdot\vec k_j,\quad \alpha_{ij} = \operatorname{softmax}_j\big(e_{ij}/\sqrt{d}\big),\quad\vec y_i = \sum_j\alpha_{ij}\vec v_j$$

> [!proof] Step 1: values and attention weights
> $\vec y_i$ depends on $\vec v_j$ linearly with coefficient $\alpha_{ij}$, and $\vec v_j$ appears in every $\vec y_i$:
> $$\frac{\partial E}{\partial\vec v_j} = \sum_{i=1}^T\alpha_{ij}\,\delta_i\qquad\Big(\text{component-wise: }\frac{\partial E}{\partial v_{jc}} = \sum_i\alpha_{ij}\frac{\partial E}{\partial y_{ic}}\Big)$$
> $\alpha_{ij}$ appears only in $\vec y_i$:
> $$\frac{\partial E}{\partial\alpha_{ij}} = \delta_i\cdot\vec v_j = \sum_c\frac{\partial E}{\partial y_{ic}}v_{jc}$$

> [!proof] Step 2: scores
> **Simplified version (exam hint: $\alpha_{ij} = e_{ij}$):**
> $$\frac{\partial E}{\partial e_{ij}} = \frac{\partial E}{\partial\alpha_{ij}} = \delta_i\cdot\vec v_j$$
> **Full softmax version:** row $i$ of $\alpha$ depends on the whole row $e_{i\cdot}$. With $s_{ik} = e_{ik}/\sqrt d$, the softmax derivative is $\frac{\partial\alpha_{ij}}{\partial s_{ik}} = \alpha_{ij}([j=k]-\alpha_{ik})$. Hence
> $$\frac{\partial E}{\partial e_{ik}} = \frac1{\sqrt d}\sum_j\frac{\partial E}{\partial\alpha_{ij}}\alpha_{ij}([j=k]-\alpha_{ik}) = \frac{\alpha_{ik}}{\sqrt d}\Big(\frac{\partial E}{\partial\alpha_{ik}} - \sum_j\alpha_{ij}\frac{\partial E}{\partial\alpha_{ij}}\Big)$$
> (For masked attention, masked entries have $\alpha=0$ and receive zero gradient.)

> [!proof] Step 3: queries and keys
> $e_{ij} = \vec q_i\cdot\vec k_j$. The query $\vec q_i$ appears in the row $e_{i1},\dots,e_{iT}$, and the key $\vec k_j$ in the column $e_{1j},\dots,e_{Tj}$:
> $$\frac{\partial E}{\partial\vec q_i} = \sum_{j=1}^T\frac{\partial E}{\partial e_{ij}}\,\vec k_j,\qquad \frac{\partial E}{\partial\vec k_j} = \sum_{i=1}^T\frac{\partial E}{\partial e_{ij}}\,\vec q_i$$

> [!proof] Step 4: weight matrices (shared over all positions → sum over positions)
> $W_q$ is used at every position: $q_{ir} = \sum_s(W_q)_{rs}x_{is}$. So $\frac{\partial q_{ir}}{\partial(W_q)_{rs}} = x_{is}$ and
> $$\frac{\partial E}{\partial(W_q)_{rs}} = \sum_{i=1}^T\frac{\partial E}{\partial q_{ir}}x_{is}\quad\Longleftrightarrow\quad\frac{\partial E}{\partial W_q} = \sum_i\frac{\partial E}{\partial\vec q_i}\vec x_i^\top$$
> and likewise
> $$\frac{\partial E}{\partial W_k} = \sum_j\frac{\partial E}{\partial\vec k_j}\vec x_j^\top,\qquad\frac{\partial E}{\partial W_v} = \sum_j\frac{\partial E}{\partial\vec v_j}\vec x_j^\top$$

> [!proof] Step 5: inputs (to continue backprop to lower layers)
> $\vec x_m$ is used to compute $\vec q_m,\vec k_m,\vec v_m$:
> $$\frac{\partial E}{\partial\vec x_m} = W_q^\top\frac{\partial E}{\partial\vec q_m} + W_k^\top\frac{\partial E}{\partial\vec k_m} + W_v^\top\frac{\partial E}{\partial\vec v_m}\qquad\blacksquare$$
> (All formulas, both the $\alpha=e$ and the softmax versions, were checked against finite differences.)

> [!example] Fully explicit simplified version ($\alpha_{ij}=e_{ij}$, so $\vec y_i=\sum_j(\vec q_i\cdot\vec k_j)\vec v_j$)
> $$\frac{\partial E}{\partial\vec v_j} = \sum_i(\vec q_i\cdot\vec k_j)\delta_i,\qquad\frac{\partial E}{\partial\vec q_i} = \sum_j(\delta_i\cdot\vec v_j)\vec k_j,\qquad\frac{\partial E}{\partial\vec k_j} = \sum_i(\delta_i\cdot\vec v_j)\vec q_i$$
> then Steps 4–5. For the squared error $E=\frac12\sum_i\|\vec y_i-\vec d_i\|^2$: $\delta_i = \vec y_i-\vec d_i$.

> [!tip] Matrix form (rows = positions)
> $dV = A^\top dY$, $\ dA = dY\,V^\top$, $\ dE = A\circ\big(dA - \operatorname{rowsum}(A\circ dA)\big)/\sqrt d$ (or $dE = dA$ in the simplified case), $\ dQ = dE\,K$, $\ dK = dE^\top Q$, $\ dW_q = dQ^\top X$, $dW_k = dK^\top X$, $dW_v = dV^\top X$, $\ dX = dQ\,W_q + dK\,W_k + dV\,W_v$.

---

## Rehearsal

> [!question]- Define the self-attention layer completely. ★
> $\vec q_i=W_q\vec x_i$, $\vec k_i=W_k\vec x_i$, $\vec v_i=W_v\vec x_i$; $e_{ij}=\vec q_i\cdot\vec k_j$; $\alpha_{ij}=\exp(e_{ij}/\sqrt{d_{attn}})/\sum_k\exp(e_{ik}/\sqrt{d_{attn}})$; $\vec y_i=\sum_j\alpha_{ij}\vec v_j$.

> [!question]- Why divide by $\sqrt{d_{attn}}$?
> The dot product of $d$-dimensional vectors with independent unit-variance components has variance $d$. Scaling restores variance 1 and avoids a saturated softmax with vanishing gradients.

> [!question]- Masked self-attention: how and why?
> Set $e_{ij}=-\infty$ for forbidden (future) positions, so $\alpha_{ij}=0$. Then $\vec y_i$ depends only on earlier inputs, which allows autoregressive generation and parallel training.

> [!question]- Multi-head attention formula and dimensions of $W_{out}$?
> $\vec y_k=W_{out}(\vec y_k^1\odot\dots\odot\vec y_k^H)$ with $\odot$ = concatenation; $W_{out}\in\mathbb R^{d_{out}\times(H\cdot d_{mid})}$; each head has its own $W_q^h,W_k^h,W_v^h$.

> [!question]- Positional encoding formula, and why sinusoids?
> $P_{k,2i}=\sin(k/n^{2i/d})$, $P_{k,2i+1}=\cos(k/n^{2i/d})$, $n=10000$, $\vec x'_k=\vec x_k+\vec P_k$. For each offset $o$ there is a linear map $M$ (block rotations) with $M\vec P_k=\vec P_{k+o}$, so relative positions are linearly accessible.

> [!question]- Layer norm formula? Where is it in GPT vs GPT-2?
> $\vec x'=\gamma(\vec x-\mu)/\sigma+\beta$ with $\mu,\sigma^2$ over the $d$ components, applied to each token vector separately. GPT: after each residual sub-block (post-LN). GPT-2: moved to the input of each sub-block (pre-LN).

> [!question]- GPT training loss and inference?
> $\vec x_k=W_e\vec u_k+\vec P_k$; loss $-\sum_{\ell=1}^{T-1}\log\vec y_\ell[a_{\ell+1}]$. Inference: run on $a_1..a_\ell$ and sample $a_{\ell+1}\sim\vec y_\ell$, repeatedly.

> [!question]- Backprop through self-attention with $\alpha_{ij}=e_{ij}$. ★
> Given $\delta_i=\partial E/\partial\vec y_i$: $\frac{\partial E}{\partial\vec v_j}=\sum_i\alpha_{ij}\delta_i$; $\frac{\partial E}{\partial e_{ij}}=\delta_i\cdot\vec v_j$; $\frac{\partial E}{\partial\vec q_i}=\sum_j\frac{\partial E}{\partial e_{ij}}\vec k_j$; $\frac{\partial E}{\partial\vec k_j}=\sum_i\frac{\partial E}{\partial e_{ij}}\vec q_i$; $\frac{\partial E}{\partial W_\star}=\sum_i\frac{\partial E}{\partial\vec\star_i}\vec x_i^\top$ for $\star\in\{q,k,v\}$; $\frac{\partial E}{\partial\vec x_m}=W_q^\top\frac{\partial E}{\partial\vec q_m}+W_k^\top\frac{\partial E}{\partial\vec k_m}+W_v^\top\frac{\partial E}{\partial\vec v_m}$.

> [!question]- With the real softmax, what changes in the backprop?
> Only the step from $\alpha$ to $e$: $\frac{\partial E}{\partial e_{ik}}=\frac{\alpha_{ik}}{\sqrt d}\big(\frac{\partial E}{\partial\alpha_{ik}}-\sum_j\alpha_{ij}\frac{\partial E}{\partial\alpha_{ij}}\big)$.
