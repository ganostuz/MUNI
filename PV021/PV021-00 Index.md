---
course: PV021 Neural Networks (Tomáš Brázdil, 2025–2026)
type: MOC
tags: [pv021, index]
---

# PV021 Neural Networks — Exam Notes (Index)

> [!warning] Exam rule (from the exam manual)
> All knowledge below is **mandatory even for grade E**. **Missing a single part automatically means F.** You may retake at official dates; only the best grade counts.

## Notes by topic (in study order)

| #   | Note                                                    | Slides  | Exam depth                                                       |
| --- | ------------------------------------------------------- | ------- | ---------------------------------------------------------------- |
| 01  | [[PV021-01 Formal Neuron and Geometry]]                 | 17–24   | formal definitions + **draw the geometry**                       |
| 02  | [[PV021-02 Network Architecture, Activity, Learning]]   | 25–40   | all definitions formally                                         |
| 03  | [[PV021-03 Expressive Power and Computability]]         | 41–54   | **proof of slide 44**; idea of 47–48; claims of 52 (no numbers)  |
| 04  | [[PV021-04 MLP and Backpropagation]]                    | 83–97   | great detail, all proofs (skip 91)                               |
| 05  | [[PV021-05 Output Activations and Error Functions]]     | 98–102  | great detail (skip 100)                                          |
| 06  | [[PV021-06 Computational Graphs and General Backprop]]  | 103–110 | great detail, **general backprop slide 109**                     |
| 07  | [[PV021-07 Gradient Descent Issues and Minibatches]]    | 112–119 | all details (117 roughly)                                        |
| 08  | [[PV021-08 Optimizers and Learning Rate]]               | 120–130 | formulas (momentum, AdaGrad, RMSProp); demonstrate one schedule  |
| 09  | [[PV021-09 Activations, Preprocessing, Initialization]] | 131–144 | **LeCun derivation ★**; one ReLU variant; init↔activation exists |
| 10  | [[PV021-10 Normalization and Regularization]]           | 145–162 | all details (no elephant)                                        |
| 11  | [[PV021-11 Convolutional Networks]]                     | 169–193 | 185–193 great detail; **derive CNN backprop**                    |
| 12  | [[PV021-12 Explainable AI]]                             | 206–235 | saliency, GradCAM, occlusion, **LIME training set**              |
| 13  | [[PV021-13 Recurrent Networks]]                         | 237–248 | all details incl. handwritten; **derive BPTT**                   |
| 14  | [[PV021-14 Attention and Transformers]]                 | 275–295 | self-attention; **derive its backprop**                          |
| 15  | [[PV021-15 Exam Drill]]                                 | all     | final checklist + rapid-fire                                     |

## Explicitly NOT required
- Slide 52: the exact neuron counts (know the claims).
- Slides 91 and 100 (logistic-specific backprop; softmax+CE gradient). Both are still included, marked optional, because they help with slide 102.
- Slide 117: only roughly what the theorem says.
- Slide 125: not all schedules (one is enough).
- Slides 141–142: not all ReLU variants (one is enough).
- Slide 143: not the exact init↔activation assignment (know that it exists).
- Slides 151–152: the elephant.
- Slide 224: TCAV. Slide 231: details may be skipped (but know LIME from slide 230).
- LSTM (omitted this year).

## How to use these notes
1. Read a note top to bottom once. Callouts mark **definitions**, **theorems**, **proofs** and **tips**.
2. Close it and reproduce every ★ item on paper.
3. Use the **Rehearsal** section at the end of each note. The answers are in collapsed callouts (`> [!question]-`): click to reveal.
4. Before the exam, go through [[PV021-15 Exam Drill]] and tick the boxes.

## Notation used throughout (same as the slides)
- $w_{ji}$ = weight **from $i$ to $j$**; $j_\leftarrow$ = predecessors of $j$; $j^\rightarrow$ = successors of $j$.
- $\xi_j$ = inner potential, $y_j = \sigma_j(\xi_j)$ = output, $y_0 = 1$ = bias input.
- $X, Y, Z$ = input, output, all neurons; $\mathcal T=\{(\vec x_k,\vec d_k)\}_{k=1}^p$ = training set.
- $\varepsilon(t)$ = learning rate; $T$ = minibatch (in SGD) or sequence length (in RNN / transformers).

## Accuracy notes
- All formulas were checked against the slides. Derivations were checked **numerically**: backprop for MLP/CNN/RNN/self-attention vs finite differences; LeCun/He variances by simulation; XOR, Boolean, RNN-example and positional-encoding tables recomputed.
- Places where the slides contain a typo or ambiguity are flagged with a ⚠ callout in the relevant note: the XOR axis labels, the slide-33 tanh, slide 176 pooling, the slide-289 index range, $j<i$ masking, and $\odot$ = concatenation.
