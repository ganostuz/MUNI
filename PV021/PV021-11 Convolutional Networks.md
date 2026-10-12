---
course: PV021 Neural Networks
topic: CNN intuition, applications, formal model, SGD, backprop (incl. derivation)
slides: 169–193 (theory 185–193 in great detail; 169–184 intuition)
exam: "185–193 everything in great detail. May be asked to DERIVE backprop for CNN. Intuition from 169–184 helps"
tags: [pv021, cnn, backprop, proofs, exam]
---

# 11 · Convolutional Networks (slides 169–193)

Up: [[PV021-00 Index]] · Prev: [[PV021-10 Normalization and Regularization]] · Next: [[PV021-12 Explainable AI]]

> [!abstract] Exam focus
> - Formal architecture, notation including $[ji]$, activity, learning, SGD, backprop (185–193), **in great detail**.
> - **Deriving backprop for CNNs** (similar to the MLP derivation in [[PV021-04 MLP and Backpropagation]]), done below.
> - Intuition from the applications (169–184).
>
> (The exam manual mentions "handwritten stuff" for 185–193; the 2025 handout has no handwritten pages there. The derivation section below covers what is typically written on the board.)

---

# Part A — Intuition and applications (slides 169–184)

## A1. Local receptive fields (slide 170)
Every neuron of a convolutional layer is connected to a field of $k\times k$ neurons in the lower layer (on the slide $5\times5$). This field is the neuron's **receptive field**. The neuron itself is "standard": it computes a weighted sum of its inputs and applies an activation function.

## A2. Stride (slide 171)
Slide the local receptive field over by one pixel (one neuron) to the right to connect to the next hidden neuron. The **"size" of the slide is the stride length**. The group of all such neurons forms a **feature map**, and **all neurons in a feature map share weights and biases!**

> [!tip] Output size of a convolution (no padding)
> Input side $N$, receptive field $k$, stride $s$ gives output side $\frac{N-k}{s}+1$. For the slide: $\frac{28-5}{1}+1 = 24$, so a $28\times28$ input gives $24\times24$ feature maps. (With zero-padding $P$: $\frac{N+2P-k}{s}+1$, standard and not on the slides.)

## A3. Feature maps (slide 172)
$28\times28$ input → first hidden layer of $3\times24\times24$ neurons (3 feature maps). **Each feature map represents a property of the input that is supposed to be spatially invariant** (e.g. "vertical edge here"). Typically there are several feature maps in a layer.

## A4. Pooling (slide 173)
Neurons in a pooling layer compute **functions of their receptive fields** (in the figure, $2\times2$ regions of a feature map):
- **Max-pooling**: maximum of the inputs.
- **L2-pooling**: square root of the sum of squares.
- **Average-pooling**: mean.

## A5. Trained receptive fields and feature maps (slides 174–175)
20 feature maps with $5\times5$ receptive fields learned on MNIST look like stroke/edge detectors. Feature maps of deeper layers respond to increasingly abstract parts of the digit.

## A6. Simple convolutional network (slides 176–177)
$28\times28$ input → **3 feature maps** ($3\times24\times24$, $5\times5$ receptive fields, stride 1) → each feature map has **its own max-pooling** ($3\times12\times12$, i.e. $2\times2$ pooling regions) → **10 output neurons**, each connected to **every** neuron of the pooling layer. Trained by backprop, which is easily adapted to CNNs.

> [!note] Parameter count (worked example; shows the power of weight sharing)
> Convolution: $3\cdot(5\cdot5 + 1) = 78$ parameters. Pooling: $0$. Dense output: $10\cdot(3\cdot12\cdot12+1) = 4330$. **Total 4408.**
> A dense layer of $3\cdot24\cdot24 = 1728$ neurons on $784$ inputs would need $1728\cdot785 \approx 1.36$ million weights instead of 78.

> [!warning] Slide wording
> Slide 176 says "max-pooling (field 5×5, stride = 1)". The $5\times5$ field with stride 1 is the **convolution** ($28\to24$). The pooling must be $2\times 2$ to go $24\to12$.

General CNN (slide 177): [convolution + non-linearity → max pooling] repeated → vectorize → fully connected layers → classification (one probability per class).

## A7. A strong MNIST network (slides 178–179)
- Two convolutional-pooling layers (20 and 40 feature maps), two dense layers (1000-1000), 10 outputs.
- ReLU for feature maps and dense layers; max-pooling; **softmax** output.
- Error: negative log-likelihood (= cross-entropy).
- SGD, minibatch size 10, learning rate 0.03, L2 regularization with "weight" $\lambda=0.1$ plus dropout with probability 1/2, 40 epochs.
- Expanded dataset: displacement by one pixel in an arbitrary direction (**data augmentation**).
- **Committee voting of 5 networks** (an ensemble).
- Result: only **33 of 10 000** test images misclassified (99.67% accuracy).

## A8. ImageNet & ILSVRC (slides 180–184)
- ImageNet: about 16 million colour images, 20 thousand classes (figures as on the slide).
- **ILSVRC** (from 2010): training set **1.2 million** images, **1000 classes**; validation 50 000; test 150 000. Many images contain several objects, so the model may name **five** classes and the correct one must be among them (**top-5 criterion**).
- **2014: GoogLeNet**, 22 layers, **93.33% top-5 accuracy**.
- **2015: ResNet**, various depths; the winner has **152 layers**; **skip connections implementing residual learning**; **3.57% top-5 error**.
- CNNs are used for classification (2D, 3D, nD, video), **segmentation** (U-Net, fully convolutional), **object detection** (bounding boxes, R-CNN), …

---

# Part B — Learning theory (slides 185–193) ★ great detail

## B1. Architecture (slide 186)

> [!definition] Layers
> Neurons are organized in layers $L_0,L_1,\dots,L_n$; connections (typically) only from $L_m$ to $L_{m+1}$.
> - **Input layer** $L_0$.
> - **Dense layer** $L_m$: each neuron of $L_m$ is connected with each neuron of $L_{m-1}$.
> - **Convolutional layer** $L_m$: neurons are organized into disjoint **feature maps**. All neurons of a feature map **share weights** but have different inputs.
> - **Pooling layer**: "neurons" are organized into **pooling maps**. All of them compute a simple **aggregate function** (such as max) and have **disjoint inputs**. Pooling after convolution is usually applied to each feature map separately, i.e. a single pooling map after each feature map.

## B2. Notation (slide 187)

> [!definition] Notation (as for MLP, plus weight sharing)
> - $X$ input neurons, $Y$ output neurons, $Z$ all neurons ($X,Y\subseteq Z$).
> - $\xi_j$, $y_j$: inner potential and output of $j$ after the computation stops; $y_0=1$ is the formal unit input.
> - $w_{ji}$: weight of the connection **from $i$ to $j$**; $w_{j0}$ is the weight from the formal unit input ($w_{j0}=-b_j$).
> - $j_\leftarrow$: all $i$ with an arc **to $j$ from $i$** (predecessors); $j^\rightarrow$: all $i$ with an arc **from $j$ to $i$** (successors).
> - $\boldsymbol{[ji]}$: the **set of all connections (pairs of neurons) sharing the weight $w_{ji}$**.

## B3. Activity (slide 188)

> [!definition] Activity
> **Dense and convolutional layers:**
> $$\xi_j = \sum_{i\in j_\leftarrow} w_{ji}y_i,\qquad y_j = \sigma_j(\xi_j)\quad(\sigma_j \text{ arbitrary differentiable})$$
> **Pooling layers** apply the pooling function:
> $$\text{max-pooling: } y_j = \max_{i\in j_\leftarrow}y_i,\qquad \text{avg-pooling: } y_j = \frac{\sum_{i\in j_\leftarrow}y_i}{|j_\leftarrow|}$$
> A CNN is evaluated layer-wise (as an MLP). For each $j\in Y$, $y_j(\vec w,\vec x)$ is the value of output neuron $j$ after evaluating the network with weights $\vec w$ and input $\vec x$.

## B4. Learning (slide 189)

> [!definition] Training set and error
> $\mathcal T = \{(\vec x_k,\vec d_k)\mid k=1,\dots,p\}$, $\vec x_k\in\mathbb R^{|X|}$, $\vec d_k\in\mathbb R^{|Y|}$, $\vec d_k = (d_{kj})_{j\in Y}$.
> Squared error (for example):
> $$E(\vec w) = \sum_{k=1}^pE_k(\vec w),\qquad E_k(\vec w) = \frac12\sum_{j\in Y}\big(y_j(\vec w,\vec x_k)-d_{kj}\big)^2$$

## B5. SGD (slide 190)

> [!algorithm] SGD for CNNs
> Compute $\vec w^{(0)},\vec w^{(1)},\dots$
> - Weights in $\vec w^{(0)}$ are randomly initialized to values close to $0$.
> - In step $t+1$: **choose (randomly) a set of training examples** $T\subseteq\{1,\dots,p\}$ and compute
> $$\vec w^{(t+1)} = \vec w^{(t)} + \Delta\vec w^{(t)},\qquad \Delta\vec w^{(t)} = -\varepsilon(t)\cdot\frac{1}{|T|}\sum_{k\in T}\nabla E_k\big(\vec w^{(t)}\big)$$
> - $T$ is a **minibatch** (of fixed size); $0<\varepsilon(t)\le1$ is the learning rate; $\nabla E_k(\vec w^{(t)})$ is the gradient of the error of example $k$.
> - The random choice of minibatches is implemented by shuffling all data and taking minibatches sequentially.
> - **Epoch** = one round through all the data.

## B6. Backprop: weights (slide 191)

> [!important] Derivatives w.r.t. weights
> Recall $\nabla E_k(\vec w^{(t)})$ is the vector of all partial derivatives $\frac{\partial E_k}{\partial w_{ji}}$. First switch from derivatives w.r.t. $w_{ji}$ to derivatives w.r.t. $y_j$:
> - For $w_{ji}$ where $j$ is in a **dense layer** (no sharing):
> $$\frac{\partial E_k}{\partial w_{ji}} = \frac{\partial E_k}{\partial y_j}\cdot\sigma_j'(\xi_j)\cdot y_i$$
> - For $w_{ji}$ where $j$ is in a **convolutional layer**:
> $$\boxed{\frac{\partial E_k}{\partial w_{ji}} = \sum_{r\ell\in[ji]}\frac{\partial E_k}{\partial y_r}\cdot\sigma_r'(\xi_r)\cdot y_\ell}$$
>   (sum over all connections $(\ell\to r)$ that share the weight $w_{ji}$).
> - Neurons of pooling layers **have no weights**.

## B7. Backprop: outputs (slide 192)

> [!important] Derivatives w.r.t. $y_j$
> - For every $j\in Y$:
> $$\frac{\partial E_k}{\partial y_j} = y_j - d_{kj}$$
>   (this holds for the squared error; for other error functions the derivative w.r.t. outputs is different).
> - For every $j\in Z\setminus Y$ such that $j^\rightarrow$ is either a **dense** or a **convolutional** layer:
> $$\frac{\partial E_k}{\partial y_j} = \sum_{r\in j^\rightarrow}\frac{\partial E_k}{\partial y_r}\cdot\sigma_r'(\xi_r)\cdot w_{rj}$$
> - For every $j\in Z\setminus Y$ such that $j^\rightarrow$ is **max-pooling**: then $j^\rightarrow = \{i\}$ for a single "max" neuron $i$ (pooling inputs are disjoint), and
> $$\frac{\partial E_k}{\partial y_j} = \begin{cases}\dfrac{\partial E_k}{\partial y_i} & \text{if } j = \arg\max_{r\in i_\leftarrow} y_r\\[4pt] 0 & \text{otherwise}\end{cases}$$
>
> The gradient can be propagated from the output layer downwards as in the MLP.

---

## B8. ★ Derivation of backprop for CNNs (be able to do this on paper)

> [!proof] Step 1: weights of a dense layer
> As for the MLP: $E_k$ depends on $w_{ji}$ only through $\xi_j = \sum_{i\in j_\leftarrow}w_{ji}y_i$, and $\xi_j$ only through $y_j$:
> $$\frac{\partial E_k}{\partial w_{ji}} = \frac{\partial E_k}{\partial y_j}\frac{\partial y_j}{\partial\xi_j}\frac{\partial\xi_j}{\partial w_{ji}} = \frac{\partial E_k}{\partial y_j}\,\sigma_j'(\xi_j)\,y_i.$$

> [!proof] Step 2: shared weights of a convolutional layer
> **Trick: untie, then sum.** Give every connection $(\ell\to r)\in[ji]$ its own private copy $w_{r\ell}$ of the weight. With all copies equal to $w_{ji}$, $E_k$ is the composition $w_{ji}\mapsto (w_{r\ell})_{r\ell\in[ji]}=(w_{ji},\dots,w_{ji})\mapsto E_k$. By the multivariate chain rule (the derivative of $f(w,w,\dots,w)$ is the sum of the partial derivatives):
> $$\frac{\partial E_k}{\partial w_{ji}} = \sum_{r\ell\in[ji]}\frac{\partial E_k}{\partial w_{r\ell}}.$$
> Each private copy appears in exactly one potential, $\xi_r = \sum_{s\in r_\leftarrow}w_{rs}y_s$, so by Step 1:
> $$\frac{\partial E_k}{\partial w_{r\ell}} = \frac{\partial E_k}{\partial y_r}\,\sigma_r'(\xi_r)\,y_\ell \quad\Longrightarrow\quad \frac{\partial E_k}{\partial w_{ji}} = \sum_{r\ell\in[ji]}\frac{\partial E_k}{\partial y_r}\,\sigma_r'(\xi_r)\,y_\ell .$$
> **Shared bias:** a bias shared by a whole feature map $F$ uses the connections $(0\to r)$ with $y_0=1$, so $\frac{\partial E_k}{\partial b} = \sum_{r\in F}\frac{\partial E_k}{\partial y_r}\sigma'_r(\xi_r)$.

> [!proof] Step 3: outputs (backward pass)
> **Output layer:** $\frac{\partial E_k}{\partial y_j} = \frac{\partial}{\partial y_j}\frac12\sum_{r\in Y}(y_r-d_{kr})^2 = y_j-d_{kj}$.
>
> **$j$ feeds a dense or convolutional layer.** $y_j$ affects $E_k$ only via $\xi_r$, $r\in j^\rightarrow$, and $\frac{\partial\xi_r}{\partial y_j} = w_{rj}$. For a conv layer, $w_{rj}$ is the value of the shared weight at the position of $j$ inside $r$'s receptive field. So
> $$\frac{\partial E_k}{\partial y_j} = \sum_{r\in j^\rightarrow}\frac{\partial E_k}{\partial y_r}\frac{\partial y_r}{\partial\xi_r}\frac{\partial\xi_r}{\partial y_j} = \sum_{r\in j^\rightarrow}\frac{\partial E_k}{\partial y_r}\,\sigma_r'(\xi_r)\,w_{rj}.$$
> Weight sharing changes nothing here: we differentiate w.r.t. $y_j$, not the weights.
>
> **$j$ feeds max-pooling.** Pooling inputs are disjoint, so $j^\rightarrow=\{i\}$ and $y_i = \max_{r\in i_\leftarrow}y_r$. Locally (assuming a unique maximum), $y_i = y_{j^*}$ with $j^*=\arg\max$, so $\frac{\partial y_i}{\partial y_j} = 1$ if $j = j^*$ and $0$ otherwise. A pooling neuron has no activation function, hence
> $$\frac{\partial E_k}{\partial y_j} = \frac{\partial E_k}{\partial y_i}\cdot\frac{\partial y_i}{\partial y_j} = \begin{cases}\frac{\partial E_k}{\partial y_i} & j = \arg\max_{r\in i_\leftarrow}y_r\\ 0&\text{otherwise.}\end{cases}$$
> The gradient is **routed only to the winner** of each pooling window.
>
> **$j$ feeds avg-pooling** (same argument): $\frac{\partial y_i}{\partial y_j} = \frac1{|i_\leftarrow|}$, so $\frac{\partial E_k}{\partial y_j} = \frac{1}{|i_\leftarrow|}\frac{\partial E_k}{\partial y_i}$: the gradient is spread equally.
>
> **Order:** compute $\frac{\partial E_k}{\partial y_j}$ layer by layer from the output down, because $j^\rightarrow$ lies in the next layer. Then use Steps 1–2 for the weights. ∎

> [!example] The same in explicit 2-D indices (verified numerically against finite differences)
> Feature map with kernel $K\in\mathbb R^{k\times k}$, bias $b$, stride $s$, input map $Y^{\text{in}}$:
> $$\xi_{a,c} = b + \sum_{p=0}^{k-1}\sum_{q=0}^{k-1}K_{p,q}\,Y^{\text{in}}_{as+p,\ cs+q},\qquad y_{a,c} = \sigma(\xi_{a,c})$$
> With $\delta_{a,c} := \frac{\partial E}{\partial\xi_{a,c}} = \frac{\partial E}{\partial y_{a,c}}\sigma'(\xi_{a,c})$:
> - $\displaystyle\frac{\partial E}{\partial K_{p,q}} = \sum_{a,c}\delta_{a,c}\,Y^{\text{in}}_{as+p,\ cs+q}$ (the $[ji]$ sum: all positions where $K_{p,q}$ is used)
> - $\displaystyle\frac{\partial E}{\partial b} = \sum_{a,c}\delta_{a,c}$
> - $\displaystyle\frac{\partial E}{\partial Y^{\text{in}}_{u,v}} = \sum_{\substack{a,c,p,q:\\ as+p=u,\ cs+q=v}}\delta_{a,c}\,K_{p,q}$ (the $\sum_{r\in j^\to}$ sum: all output neurons whose receptive field contains $(u,v)$)

## B9. Summary (slide 193)

- Conv nets are nowadays the most used networks in image processing, and in other areas where the input has local, "spatially" invariant properties. (2024 update: vision transformers are gradually taking over.)
- They are typically trained by gradient descent and its modifications (such as Adam).
- **Weight sharing allows (very) deep architectures.**
- They are typically extended with more adjustments and tricks in their topologies.

---

## Rehearsal

> [!question]- What is $[ji]$ and why is it needed?
> The set of all connections sharing the weight $w_{ji}$. A shared weight affects $E$ through every connection that uses it, so its gradient is the sum over $[ji]$.

> [!question]- Write $\partial E_k/\partial w_{ji}$ for a dense and for a conv layer.
> Dense: $\frac{\partial E_k}{\partial y_j}\sigma'_j(\xi_j)y_i$. Conv: $\sum_{r\ell\in[ji]}\frac{\partial E_k}{\partial y_r}\sigma'_r(\xi_r)y_\ell$.

> [!question]- Write $\partial E_k/\partial y_j$ in all three cases.
> $j\in Y$: $y_j-d_{kj}$. $j^\to$ dense/conv: $\sum_{r\in j^\to}\frac{\partial E_k}{\partial y_r}\sigma'_r(\xi_r)w_{rj}$. $j^\to=\{i\}$ max-pooling: $\frac{\partial E_k}{\partial y_i}$ if $j=\arg\max_{r\in i_\leftarrow}y_r$, else 0.

> [!question]- Derive the conv-layer weight gradient.
> Untie the shared weight into per-connection copies $w_{r\ell}$; the derivative of $f(w,\dots,w)$ is the sum of the partials, $\sum_{r\ell\in[ji]}\partial E_k/\partial w_{r\ell}$; each partial is the MLP formula $\frac{\partial E_k}{\partial y_r}\sigma'_r(\xi_r)y_\ell$.

> [!question]- Max- vs avg-pooling backward?
> Max: the whole gradient goes to the argmax input, 0 to the others. Avg: each input gets $\frac1{|i_\leftarrow|}$ of the gradient.

> [!question]- Output size for 28×28 input, 5×5 field, stride 1? Then 2×2 pooling?
> $24\times24$; then $12\times12$.

> [!question]- What is the SGD update in the CNN slides and what is an epoch?
> $\Delta\vec w=-\varepsilon(t)\frac1{|T|}\sum_{k\in T}\nabla E_k$; an epoch is one pass through all the data.

> [!question]- Key ILSVRC facts?
> 1.2M train images, 1000 classes, top-5 criterion. GoogLeNet 2014: 22 layers, 93.33% top-5. ResNet 2015: 152 layers, skip connections, 3.57% top-5 error.
