## History window slide ==69== right now
history end now notation

---
## Notation
$X$ set of all input neurons
$Y$ set of all output neurons
$Z$ set of all neurons

Let $j$ be neuron, then:
- $w_{ij}$ is weight from neuron i to neuron j
- $j\leftarrow$ is set of input neurons
- $j\rightarrow$ is set of output neurons
- inner potentional of neuron $=\sum_{i\in j\leftarrow}{w_{ji}*y_i}$
- $\xi_j = \sum_{i \in j_{\leftarrow}} w_{ji} \, y_i$
- $y_j = \sigma_j(\xi_j)$
training sets are often of the format: $\{(\vec{x}_k,\vec{d}_k)|k=1,...,p\}$
## Basic error function
Squared error function
$$E(\vec{w}) = \frac{1}{p} \sum_{k=1}^{p} E_k(\vec{w})$$

where
$$E_k(\vec{w}) = \frac{1}{2} \sum_{j \in Y} \left( y_j(\vec{w}, \vec{x}_k) - d_{kj} \right)^2$$
## Batch algorithm (gradient descent)
always points to local maxima

> [!TODO]+ TODO
>  - look into taylor and Vesely again
>  - write gradient descent yourself
 >  - back propagation
 >  - write error function gradient yourself
> - look into multivariable, vector calculus (partial derivatives mainly)
 > 	 - mainly chain rule ...
>  - learn up to slide 110
