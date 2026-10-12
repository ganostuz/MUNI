---
course: PV021 Neural Networks
topic: XAI – taxonomy, filter/feature-map visualization, maximizing input, gradient saliency, SmoothGrad, GradCAM, occlusion, LIME
slides: 206–235
exam: "Everything in detail; especially 216–235 (saliency, GradCAM, occlusion, LIME) with all math + intuition. Slide 231 details may be skipped, but know how LIME works from slide 230 (esp. the TRAINING SET!). TCAV (224) not needed"
tags: [pv021, xai, gradcam, lime, saliency, exam]
---

# 12 · Explainable AI (XAI) (slides 206–235)

Up: [[PV021-00 Index]] · Prev: [[PV021-11 Convolutional Networks]] · Next: [[PV021-13 Recurrent Networks]]

---

## 1. What is XAI? (slides 206–207)

Slide 206 shows the desired properties of XAI: understandable, comprehensible, transparent, interpretable, intelligible, responsible, interactive, explainable, revisable, trustworthy, accountable.

> [!definition] Definitions
> **IBM:** "Explainable artificial intelligence (XAI) is a set of processes and methods that allows human users to comprehend and trust the results and output created by machine learning algorithms."
> **ISO/IEC TR 29119-11:2020:**
> - **Interpretability**: the level of understanding **how the underlying (AI) technology works**.
> - **Explainability**: the level of understanding **how the AI-based system came up with a given result**.

## 2. Taxonomy of XAI methods (slides 208–210)

The goal is to understand **how and why the network does what it does**. Only **classification** models are considered.

```
                    Explainability method
                 ┌──────────┴──────────┐
               Scope            Time of information extraction
            ┌────┴────┐            ┌────────┴────────┐
          Local     Global      Intrinsic         Post-hoc
                                    │            ┆       │
                              Model-specific ◀┄┄┘  Model-agnostic
```
- **Scope:** *local* (explains one prediction / one input) vs *global* (explains the model as a whole).
- **Time of extraction:** *intrinsic* (the model is interpretable by design) vs *post-hoc* (explain an already trained model).
- Intrinsic methods are **model-specific**. Post-hoc methods are **model-agnostic** or (dashed line) model-specific.

Methods covered, by principle (slide 210):
1. **Visualize weights and feature maps**
2. **Visualize the most important inputs for a given class**
3. **Visualize the effect of input perturbations on the output**
4. **Construct an interpretable surrogate model**

> [!tip] Classifying the methods below (my summary, consistent with the taxonomy)
> | Method | Scope | Type | Needs model internals? |
> |---|---|---|---|
> | Filter / feature-map visualization | global (filters), local (maps for one image) | post-hoc | model-specific |
> | Maximizing input | global (per class) | post-hoc | model-specific (gradients) |
> | Gradient saliency, SmoothGrad | local | post-hoc | model-specific (gradients) |
> | GradCAM | local | post-hoc | model-specific (CNN + gradients) |
> | Occlusion | local | post-hoc | model-agnostic (only outputs) |
> | LIME | local | post-hoc | model-agnostic (surrogate model) |

---

## 3. Visualizing weights and feature maps (slides 211–213)

- **AlexNet, first conv layer:** 64 filters of depth 3 (RGB). Each filter's RGB channels combined into one RGB image of size $11\times11\times3$. They look like oriented edges, stripes, colour blobs (Gabor-like).
- **Feature maps** (slide 212): the activations of the conv/pool layers for one input image (a dog); deeper layers are coarser and more abstract.
- **Radar target classification** (slide 213): feature maps and convolution kernels (256 kernels of $3\times3$) of a CNN on synthetic-aperture radar (SAR) images. SAR is used to create 2D images or 3D reconstructions of objects such as landscapes.

## 4. Maximizing input (slides 214–215)

> [!definition] Class-representative input
> Find the most "representative" input vector for a given class.
> - Assume a trained model giving a score for each class.
> - Let $\xi_i(\vec x)$ be the **inner potential of output neuron $i\in Y$** for the network input $\vec x$.
> - **Maximize**
> $$\xi_i(\vec x) - \lambda\|\vec x\|_2^2$$
>   over all input vectors $\vec x$. The $\lambda\|\vec x\|_2^2$ term keeps the image from growing without bound.
> - The maximizing vector is computed by **gradient ascent**. With the weights **fixed**, we optimize the input:
> $$\vec x \leftarrow \vec x + \eta\big(\nabla_{\vec x}\xi_i(\vec x) - 2\lambda\vec x\big)$$
> - The result is the most "representative" input vector of the class represented by neuron $i$ (slide 215: dumbbell, cup, dalmatian).
> - Using the potential $\xi_i$ rather than the softmax output means we maximize the evidence for class $i$ itself, not "being less like the other classes".

---

## 5. Input-specific saliency maps (slide 216)

**Goal:** label the features in a **given input** that are "most important" for the output of the network.
- **Gradient based:** gradient saliency maps, GradCAM, SmoothGrad, …
- **Occlusion based:** simple occlusion maps, LIME, …

## 6. Gradient-based saliency (slides 217–220)

> [!definition] Gradient saliency
> - Fix an output neuron $i$ and an input vector $\vec x$.
> - **Idea:** rank every input neuron $k\in X$ by its influence on the value $\xi_i(\vec x)$. The input values are **fixed**.
> - For every input neuron $k\in X$ consider
> $$\left|\frac{\partial\xi_i}{\partial y_k}(\vec x)\right|$$
>   to measure the importance of input $y_k$ for the output potential $\xi_i$ **with respect to the particular input vector $\vec x$**.
> - **Interpretation:** saliency comes from a **surrogate local linear model** given by the first-order **Taylor approximation**:
> $$\xi_i(\vec x') \approx \xi_i(\vec x) + \Big(\frac{\partial\xi_i}{\partial X}(\vec x)\Big)(\vec x'-\vec x)$$
>   where $\frac{\partial\xi_i}{\partial X}$ is the vector of all partial derivatives $\frac{\partial\xi_i}{\partial y_k}$, $k\in X$. A large $|\partial\xi_i/\partial y_k|$ means a small change of pixel $k$ changes the class score a lot.
> - The derivatives are computed by **backpropagation down to the input layer** (one backward pass, weights not updated).
> - For RGB images there are 3 derivatives per pixel. Slide 219 shows "gradients across RGB channels" and "max gradients" (the maximum over channels of the absolute value), giving one value per pixel.

> [!warning] Weakness (slide 219)
> Gradient saliency maps are **quite noisy**: the signal is spread and does not say much about the perception of the object (the owl).

> [!definition] SmoothGrad (slide 220)
> Do the following several times:
> - **add noise** to the input image;
> - compute a saliency map.
>
> **Average** the resulting saliency maps. The noise averages out and the object (e.g. an obelisk) stands out.

---

## 7. GradCAM (slides 221–223)

> [!definition] GradCAM
> - Consider a convolutional network and fix an input image $I$. All values of neurons $y_j$ are computed on $I$.
> - Fix a **convolutional layer** $L$ consisting of feature maps $F^1,\dots,F^k$. Each $F^\ell$ is the set of neurons belonging to feature map $\ell$. Slightly abusing notation, $F^\ell(I)$ denotes the tensor (matrix) of all values of the neurons in $F^\ell$ on $I$.
> - Fix an output neuron $i\in Y$ with inner potential $\xi_i$. Compute the **average importance** of $F^\ell(I)$:
> $$\alpha_i^\ell = \frac{1}{|F^\ell|}\sum_{j\in F^\ell}\frac{\partial\xi_i}{\partial y_j}\big(F^\ell(I)\big)$$
> - The final **gradCAM heat map** for $L$:
> $$M_i^L = \mathrm{ReLU}\Bigg(\sum_{\ell=1}^k\alpha_i^\ell\,F^\ell(I)\Bigg)$$

> [!tip] Intuition: why each piece
> - $\alpha^\ell_i$ = **global average of the gradient** over feature map $\ell$: how much "more of feature $\ell$ anywhere" increases the class-$i$ score. It is one scalar weight per feature map.
> - $\sum_\ell\alpha_i^\ell F^\ell(I)$ = a weighted sum of the feature maps: **where in the image** the class-relevant features are active.
> - $\mathrm{ReLU}$ keeps only regions with a **positive** influence on class $i$ (evidence *for* the class).
> - The **last convolutional layer** is used because it is the best compromise between high-level semantics and spatial information (dense layers lose the spatial layout).
> - The map has the spatial size of the feature maps, so it is **upsampled/smoothed** to the image size and overlaid.

> [!example] GradCAM on VGG16 (slides 222–223)
> - VGG16: $224\times224\times3$ → conv blocks ($224\times224\times64$, $112\times112\times128$, $56\times56\times256$, $28\times28\times512$, $14\times14\times512$) with max pooling between blocks → $7\times7\times512$ → fully connected + ReLU ($1\times1\times4096$ twice) → softmax $1\times1\times1000$.
> - Use the **last convolutional layer** (Block 5, $14\times14\times512$ maps). The raw GradCAM map is therefore about $14\times14$.
> - Cat example, left to right: the image (resized to $224\times224$ to fit VGG16) → the raw GradCAM heat map for the last conv layer and the class "cat" → the rescaled and smoothed heat map → the overlay on the image.

> [!note]- TCAV (slide 224) — not needed for the exam
> Testing with Concept Activation Vectors: separate a layer's activations $f_l$ of concept examples (e.g. "striped") from random examples by a linear classifier. Its normal is the concept vector $v_C^l$. The sensitivity of class $k$ to the concept is the directional derivative $S_{C,k,l}(x) = \nabla h_{l,k}(f_l(x))\cdot v_C^l$ (e.g. how much "stripes" matter for "zebra").

---

## 8. Occlusion (slides 225–227)

> [!definition] Occlusion
> - **Systematically cover parts of the input image** (e.g. slide a grey square across the image).
> - **Observe the effect on the output value** (e.g. $\xi_i$ or the class probability).
> - **Find the regions with the largest effect**: where covering makes the score drop most is what the network relies on.

Examples: Pomeranian (covering the face kills the prediction), car wheel, Afghan hound; a harmonica (the heat map highlights the instrument near the mouth).
Properties: very intuitive and **model-agnostic** (it only needs forward passes). It is expensive (one forward pass per occluder position), and the result depends on the patch size and stride.

---

## 9. LIME — Local Interpretable Model-agnostic Explanations (slides 228–235)

> [!summary] Outline for images (slide 228)
> Fix an image $I$ to be explained.
> 1. Consider **superpixels** of $I$ as interpretable components.
> 2. Construct a **linear model approximating the network around the image $I$**, with weights corresponding to the superpixels.
> 3. Select the superpixels with weights of **large magnitude** as the important ones.

> [!definition] Superpixels and sub-images (slide 229)
> Let $P_1,\dots,P_\ell$ be all superpixels of $I$ (contiguous patches of similar pixels). Consider binary vectors
> $$\vec x = (x_1,\dots,x_\ell)\in\{0,1\}^\ell$$
> Each such vector determines a **"subimage" $I[\vec x]$** of $I$, obtained by **removing all $P_k$ with $x_k = 0$** (e.g. greying them out). The all-ones vector gives $I$ itself.

> [!important] LIME training set and model (slide 230): KNOW THE TRAINING SET
> - Fix an output neuron $i$. Let $\xi_i(J)$ be the inner potential of output neuron $i$ for the input image $J$.
> - Given the image $I$ to be interpreted, consider the **training set**
> $$\mathcal T = \Big\{\big(\vec x_1,\ \xi_i(I[\vec x_1])\big),\ \dots,\ \big(\vec x_p,\ \xi_i(I[\vec x_p])\big)\Big\}$$
>   - **inputs** $\vec x_h = (x_{h1},\dots,x_{h\ell})$ are (some) binary vectors of $\{0,1\}^\ell$, e.g. **randomly selected**, i.e. which superpixels are kept;
>   - **targets** are the **network's own** output potentials for the corresponding perturbed subimages $I[\vec x_h]$, **not** ground-truth labels.
> - **Train a linear model (ADALINE)** with weights $w_0,w_1,\dots,w_\ell$ on $\mathcal T$. Intuitively, the linear model approximates the network on "subimages" of $I$ obtained by removing some superpixels.
> - **Inspect the weights (magnitude and sign).** $w_k$ is the contribution of superpixel $P_k$ to class $i$: large positive means strong evidence for class $i$, negative means evidence against.

> [!definition]- Precise objective (slide 231, details may be skipped)
> Train the linear model (ADALINE) $F$ with weights $\vec w = w_0,w_1,\dots,w_\ell$ on $\mathcal T$, minimizing the **weighted mean-squared error**
> $$\mathcal E(\vec w) = \frac1p\sum_{k=1}^p\pi_k\cdot\Big(F(\vec x_k) - \xi_i\big(I[\vec x_k]\big)\Big)^2 + \Omega(\vec w)$$
> where the weights are
> $$\pi_k = \exp\left(\frac{-\Big(1-\sqrt{1-(s_k/\ell)}\Big)^2}{2\nu^2}\right)$$
> - $s_k$ = the number of elements of $\vec x_k$ equal to zero (removed superpixels), $\ell$ = the number of superpixels.
> - $\nu$ determines how much the perturbed images are taken into account. **Small $\nu$** makes $\pi_k$ close to zero for $\vec x_k$ with many zeros, so only images close to $I$ count. This makes the model **local**.
> - $\Omega(\vec w)$ is a **regularization term** making the number of **non-zero weights as small as possible** (a sparse, readable explanation).
> - (Checked: $1-\sqrt{1-s_k/\ell}$ is exactly the **cosine distance** between $\vec x_k$ and the all-ones vector, i.e. the original image. So $\pi_k$ is a Gaussian kernel in that distance.)

> [!example] Examples (slides 232–235)
> - **Tree frog** (original $P(\text{tree frog}) = 0.54$). Perturbed instances: one keeping the head gives $0.85$; one with almost everything removed gives $0.00001$; one with a small part removed gives $0.52$. The explanation is the superpixels of the **frog's head**.
> - **Dog with a guitar**: separate explanations for "electric guitar" (the fretboard), "acoustic guitar" (the body) and "Labrador" (the dog's face).
> - **Husky classified as wolf**: the explanation is the **snow** in the background. The model learned a **spurious correlation** (wolves were photographed on snow). This shows how XAI reveals a bad model.

---

## Rehearsal

> [!question]- Interpretability vs explainability (ISO)?
> Interpretability: understanding how the underlying AI technology works. Explainability: understanding how the system came up with a given result.

> [!question]- Taxonomy dimensions of XAI methods?
> Scope: local vs global. Time of extraction: intrinsic vs post-hoc. Model-specific vs model-agnostic (intrinsic methods are model-specific; post-hoc methods can be either).

> [!question]- Maximizing input: objective and method?
> $\max_{\vec x}\ \xi_i(\vec x)-\lambda\|\vec x\|_2^2$ by gradient ascent on the input (weights fixed).

> [!question]- Gradient saliency formula and its justification?
> $|\partial\xi_i/\partial y_k(\vec x)|$ for each input $k$. Justification: the first-order Taylor expansion $\xi_i(\vec x')\approx\xi_i(\vec x)+\frac{\partial\xi_i}{\partial X}(\vec x)(\vec x'-\vec x)$ is a local linear surrogate, and the gradient entries are its weights.

> [!question]- SmoothGrad?
> Add noise to the image several times, compute a saliency map each time, and average the maps.

> [!question]- GradCAM formulas and the role of each part?
> $\alpha_i^\ell=\frac1{|F^\ell|}\sum_{j\in F^\ell}\frac{\partial\xi_i}{\partial y_j}$ (globally averaged gradient = importance of map $\ell$). $M_i^L=\mathrm{ReLU}(\sum_\ell\alpha_i^\ell F^\ell(I))$: the weighted sum locates the evidence and ReLU keeps positive evidence. Use the last conv layer, then upsample.

> [!question]- Occlusion in three steps?
> Systematically cover parts of the image, observe the effect on the output, and find the regions with the largest effect.

> [!question]- LIME: what exactly is the training set? ★
> $\mathcal T=\{(\vec x_h,\ \xi_i(I[\vec x_h]))\}_{h=1}^p$. Inputs are random binary masks over the $\ell$ superpixels; targets are the network's potential for class $i$ on the masked image $I[\vec x_h]$. Fit a linear model (ADALINE) $w_0+\sum_k w_kx_k$ (weighted MSE with locality weights $\pi_k$ + sparsity $\Omega$) and read off the large weights.

> [!question]- What does $\nu$ do in LIME's $\pi_k$?
> It is the width of the locality kernel. A small $\nu$ means images with many removed superpixels get weight ≈ 0.

> [!question]- What did the husky/wolf LIME example reveal?
> The classifier used the snow background (a spurious correlation), not the animal.
