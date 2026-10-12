---
course: PV021 Neural Networks
topic: Batch normalization, generalization, early stopping, network size, ensembles, dropout, weight decay
slides: 145–162
exam: "All details (slides 151–152, the elephant, not needed)"
tags: [pv021, regularization, batchnorm, dropout, exam]
---

# 10 · Batch Normalization and Regularization (slides 145–162)

Up: [[PV021-00 Index]] · Prev: [[PV021-09 Activations, Preprocessing, Initialization]] · Next: [[PV021-11 Convolutional Networks]]

---

## 1. Batch normalization (slides 145–147)

> [!note] Intuition (slide 145)
> Instead of keeping mean $=0$ and variance $=1$ implicitly through a clever weight initialization, we can **renormalize the values of neurons** throughout the layers.
> The output values of the $\ell$-th layer are the inputs of the sub-network formed by all layers above it. **Why not standardize them as we did with the input data?** For that we need a "dataset" of values of the $\ell$-th layer, which the minibatch provides.

> [!definition] Batch normalization (slide 146)
> Consider the $\ell$-th layer with $n$ neurons and a batch of training examples $\{(\vec x_k,\vec d_k)\mid k=1,\dots,p\}$ (typically a **minibatch**).
> 1. For every $k=1,\dots,p$, compute the values of the neurons in the $\ell$-th layer for input $\vec x_k$, giving the vector $\vec z_k = (z_{k1},\dots,z_{kn})$.
> 2. Standardize all components of the vectors $\vec z_k$ to mean $=0$ and variance $=1$, giving **normalized vectors** $\hat z_1,\dots,\hat z_p$.
> 3. For every $k=1,\dots,p$, output
> $$\vec\gamma\cdot\hat z_k + \vec\delta$$
>    as the output of the $\ell$-th layer instead of $\vec z_k$ (component-wise product). $\vec\gamma$ and $\vec\delta$ are **new trainable weights**: they let the network undo or rescale the normalization if that helps.

> [!definition] Normalization formulas (slide 147)
> **During training**, the normalized vectors are computed per component $i$ over the batch:
> $$\hat z_{ki} = \frac{z_{ki}-\mu_i}{s_i},\qquad \mu_i = \frac1p\sum_{k=1}^pz_{ki},\qquad s_i = \sqrt{\frac1p\sum_{k=1}^p(z_{ki}-\mu_i)^2}$$
> **During inference**, where there is just a single value $\vec z$ of layer $\ell$ for an input $\vec x$, we use $\mu_i$ and $s_i$ estimated **on a population** (e.g. a larger sample of the training set).
> (In practice a small constant is added to $s_i$ or inside the square root to avoid division by 0.)

> [!tip] Batch norm vs layer norm
> Batch norm normalizes **each neuron across the examples in the batch**. Layer norm (in transformers, [[PV021-14 Attention and Transformers]]) normalizes **across the components of one vector**. Layer norm does not depend on the batch, and training and inference behave the same.

---

## 2. Generalization (slide 149)

> [!definition] Generalization
> **Generalization** = the ability to cope with new, unseen instances.
> - Data are mostly noisy, so it is not a good idea to fit them exactly.
> - In function approximation, the network should not return exactly the results in the training set.
> - Formally, the training set is typically assumed to be generated as
> $$d_{kj} = g_j(\vec x_k) + \Theta_{kj}$$
>   where $g_j$ is the "underlying" function for output neuron $j\in Y$ and $\Theta_{kj}$ is **random noise**. **The network should fit $g_j$, not the noise.**
> - Methods improving generalization are called **regularization methods**.

## 3. Regularization (slides 150, 153)

Regularization is a big issue in neural networks: they use a huge number of parameters and are therefore very **susceptible to overfitting**.
*von Neumann: "With four parameters I can fit an elephant, and with five I can make him wiggle his trunk." … and what can you fit with 40GB of parameters??* (The elephant parametrization on slides 151–152 is not needed.)

---

## 4. Early stopping (slides 154–155)

> [!definition] Early stopping
> **Stop learning before it reaches a minimum of the error $E$.**
> - In many applications the error function is not the main thing we want to optimize. E.g. a trading system should maximize profit, not minimize a strange error function designed to be easily differentiable.
> - As noted, minimizing $E$ completely is not good for generalization.
>
> **Standard approach: train on one set, stop on another.** Divide the dataset into:
> - **training set** (e.g. 60%): train the network here;
> - **validation set** (e.g. 20%): used to stop the training;
> - **test set** (e.g. 20%): used to evaluate the final model.
>
> **Stopping rules:**
> - Observe $E$ (or any other function of interest) on the **validation set**; if it **does not improve for the last $k$ steps, stop**.
> - Alternatively, observe the **gradient**; if it is small for some time, stop. (Some studies have shown that this traditional rule is not too good: the gradient may be larger close to minimum values. On the other hand, $E$ does not have to be evaluated, which saves time.)
> - To compare models use ML techniques such as various types of **cross-validation**.

## 5. Size of the network (slide 156)

> [!note] Network size
> A similar problem to that of training duration:
> - A **too small** network cannot capture the intrinsic properties of the training set.
> - **Large networks overfit faster.**
>
> Solution: the "optimal" number of neurons :-)
> - there are some (useless) theoretical bounds;
> - there are algorithms dynamically adding/removing neurons (not much used nowadays);
> - **in practice: start with an existing network solving a similar problem.**
> - Ancient rule of thumb if truly desperate: **number of neurons ≈ ten times less than the number of training instances**. Experiment, experiment, experiment.

## 6. Feature extraction (slide 157)

In a two-layer network, the **hidden neurons are supposed to represent "patterns" in the inputs**. Example: a **64-2-3** network for letter classification (E, L, F on $8\times8$ grids). The learned input-to-hidden weights, visualized as $8\times8$ images, show which input patterns (strokes) the two hidden neurons detect.

---

## 7. Ensemble methods and bagging (slide 158)

> [!definition] Ensembles
> Techniques for reducing generalization error by **combining several models**. They work because different models usually do **not all make the same errors** on the test set.
> **Idea:** train several different models separately, then have all of them **vote** on the output for test examples.

> [!definition] Bagging
> - Generate $k$ training sets $T_1,\dots,T_k$ by **sampling from $\mathcal T$ uniformly with replacement**. If the number of samples is $|\mathcal T|$, then on average $|T_i| = (1-1/e)|\mathcal T|$, counting **distinct** examples ($\approx 63.2\%$).
> - For each $i$, train a model $M_i$ on $T_i$.
> - Combine the outputs of the models: **averaging** for regression, **(majority) voting** for classification.

> [!proof]- Why $1-1/e$ (checked by simulation: 0.632)
> Draw $P=|\mathcal T|$ times with replacement. A fixed example is never drawn with probability $(1-\frac1P)^P\to e^{-1}$. So the expected fraction of distinct examples drawn is $1-(1-\frac1P)^P\approx 1-\frac1e$.

---

## 8. Dropout (slides 159–160)

> [!definition] Dropout algorithm (slide 159)
> In **every step of gradient descent**:
> - choose randomly a set $N$ of neurons, each included **independently with probability $1/2$** (in practice other probabilities are used too);
> - do the forward and backward propagation **only using the selected neurons** (the weights of the other neurons stay unchanged in this step).

> [!note] Dropout vs bagging (slide 159)
> - Dropout **resembles bagging**: a large ensemble of networks is trained "at once" on parts of the data.
> - It is **not exactly bagging**: the models **share parameters**, each inheriting a different subset of the parent network's parameters. Sharing makes it possible to represent an **exponential number of models** with a tractable amount of memory.
> - In bagging each model is trained to convergence on its own training set, which would be infeasible for large networks and training sets.

> [!definition] Dropout details (slide 160)
> Inner potential of neuron $j$ **without dropout**:
> $$\xi_j = \sum_{i\in j_\leftarrow} w_{ji}y_i$$
> Inner potential of neuron $j$ **with dropout**:
> $$r_i\sim\mathtt{Bernoulli}(1/2)\ \text{ for all } i\in j_\leftarrow\setminus\{0\},\qquad \xi_j = \sum_{i\in j_\leftarrow} w_{ji}(r_iy_i)$$
> (Randomly chosen neurons are masked out. The bias input $0$ is never dropped.)
> **During inference** do not drop out neurons, and **multiply the values of neurons by $1/2$**. This compensates for the fact that without dropout there are twice as many active neurons, so the expected input of $\xi_j$ matches training: $\mathbb E[r_iy_i]=\tfrac12y_i$.
> (In general, with keep-probability $q$, multiply by $q$ at inference. Libraries often implement "inverted dropout", dividing by $q$ during training instead.)

---

## 9. Weight decay and L2 regularization (slide 161)

> [!definition] Weight decay
> Generalization can be improved by removing "unimportant" weights. Penalizing large weights gives a stronger indication of their importance.
> In every step, decrease the weights **multiplicatively**:
> $$w_{ji}^{(t+1)} = (1-\zeta)\,w_{ji}^{(t)} - \varepsilon\cdot\frac{\partial E}{\partial w_{ji}}\big(\vec w^{(t)}\big)$$
> **Intuition:** unimportant weights are pushed to $0$; important weights survive the decay (their gradient keeps restoring them).

> [!theorem] Equivalence with L2 regularization
> Weight decay is equivalent to gradient descent with a **constant learning rate $\varepsilon$** and the error function
> $$E'(\vec w) = E(\vec w) + \frac{\zeta}{2\varepsilon}(\vec w\cdot\vec w)$$
> Here $\frac{\zeta}{2\varepsilon}(\vec w\cdot\vec w)$ is the **L2 regularization** that penalizes large weights.

> [!proof] Proof
> $\dfrac{\partial E'}{\partial w_{ji}} = \dfrac{\partial E}{\partial w_{ji}} + \dfrac{\zeta}{2\varepsilon}\cdot 2w_{ji} = \dfrac{\partial E}{\partial w_{ji}} + \dfrac{\zeta}{\varepsilon}w_{ji}$. A GD step on $E'$ with rate $\varepsilon$:
> $$w_{ji}^{(t+1)} = w_{ji}^{(t)} - \varepsilon\Big(\frac{\partial E}{\partial w_{ji}} + \frac{\zeta}{\varepsilon}w_{ji}^{(t)}\Big) = (1-\zeta)w_{ji}^{(t)} - \varepsilon\frac{\partial E}{\partial w_{ji}}\big(\vec w^{(t)}\big).\ \blacksquare$$
> (A constant learning rate is used only to illustrate the equivalence. Both methods can be combined with other learning algorithms, e.g. AdaGrad. With adaptive methods the two are no longer equivalent.)

## 10. More (slide 162)

There are many more practical tips, optimization methods and regularization methods; see the *Deep Learning* book (deeplearningbook.org).

---

## Rehearsal

> [!question]- Describe batch normalization (training and inference).
> For layer $\ell$ and a minibatch, compute $\vec z_k$. Normalize each component: $\hat z_{ki}=(z_{ki}-\mu_i)/s_i$ with batch mean $\mu_i$ and std $s_i$. Output $\vec\gamma\cdot\hat z_k+\vec\delta$ with trainable $\vec\gamma,\vec\delta$. At inference use $\mu_i,s_i$ estimated on a population (e.g. a larger training sample).

> [!question]- What is the formal model behind "generalization"?
> $d_{kj}=g_j(\vec x_k)+\Theta_{kj}$ (underlying function + noise). Fit $g_j$, not the noise.

> [!question]- Early stopping: data splits and stopping rule?
> Train / validation / test, e.g. 60/20/20. Stop when the validation error has not improved for the last $k$ steps (or when the gradient is small for a while, which is less reliable).

> [!question]- Bagging: how are the sets formed and combined? Expected unique fraction?
> Sample $|\mathcal T|$ times uniformly with replacement for each $T_i$; train $M_i$; average (regression) or majority-vote (classification). About $1-1/e\approx63\%$ of the examples are distinct.

> [!question]- Dropout: training and inference formulas. Why is it like bagging?
> Training: $r_i\sim\mathrm{Bernoulli}(1/2)$ and $\xi_j=\sum_i w_{ji}(r_iy_i)$, with a new mask each GD step. Inference: no dropout, multiply values by $1/2$. It is like bagging because it trains an exponential ensemble of sub-networks, but these share parameters.

> [!question]- Show that weight decay = L2 regularization.
> $E'=E+\frac{\zeta}{2\varepsilon}\vec w\cdot\vec w$ gives the GD step $w-\varepsilon(\partial E/\partial w+\frac\zeta\varepsilon w)=(1-\zeta)w-\varepsilon\,\partial E/\partial w$.

> [!question]- Rule of thumb for network size?
> Start with an existing network for a similar problem. Ancient rule: number of neurons ≈ (number of training instances)/10.
