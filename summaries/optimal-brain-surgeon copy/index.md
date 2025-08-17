# Optimal Brain Surgeon

- **Title:** Second order derivatives for network pruning: Optimal brain surgeon
- **Authors:** Hassibi Babak, and David Stork
- **Link to paper**: https://proceedings.neurips.cc/paper/1992/file/303ed4c69846ab36c2904d3ba8573050-Paper.pdf
- **Published in:** Advances in neural information processing systems 5 (1992)
- **Year**: 1992



## What

The authors propose *Optimal Brain Surgeon (OBS)*, a method for pruning neural networks that generalizes the *Optimal Brain Damage (OBD)* method [1]. Like OBD, it uses second-order derivatives of the loss function and the criterion of *minimal loss increase* to identify the least important weights. However, unlike OBD, OBS does not assume a diagonal Hessian and doesn't require retraining after each pruning step.



## Why

Reducing the number of parameters in a neural network—known as pruning—can improve generalization and make models more efficient. The OBD paper showed that using second-order information leads to more effective pruning than simple magnitude-based approaches.

However, OBD uses a diagonal approximation of the Hessian, which can be suboptimal with respect to the pruning criterion of *minimal loss increase*. OBS uses weaker assumptions, leading to a principled generalization of OBD and magnitude-based pruning. Empirically, it performs better.



## Details

OBS proceeds iteratively, removing one weight at a time while adjusting the others to minimize the induced loss increase. The weight to prune is the one whose removal causes the smallest loss increase *after adjusting all other weights*.

To make this tractable, OBS uses a second-order approximation of the error $E$, where a perturbation $\delta W$ causes a change in loss/error:

$$
\delta E \approx G^T \delta W + \frac{1}{2} \delta W^T H \delta W \approx \frac{1}{2} \delta W^T H \delta W
$$

where:
- $G$ is the gradient (which vanishes at convergence)
- $H$ is the Hessian matrix.

We seek the weight $w_q$ whose removal results in the least increase in loss, formulated as:

$$
\min_q \{\min_{\delta W} \delta W^T H \delta W \ \  \text{subject to} \ \  e_q^T \delta W + w_q = 0 \Leftrightarrow \delta W_q = - w_q\}
$$

Using the method of Lagrange multipliers, the optimal update when removing $w_q$ is:

$$
\delta W = - \frac{w_q}{[H^{-1}]_{qq}} H^{-1} e_q
$$

with a corresponding loss increase of:

$$
L_q = \frac{1}{2} \frac{w_q^2}{[H^{-1}]_{qq}}
$$

So, given an estimate of $H^{-1}$, the algorithm removes the weight $w_{q^*}$ where:

$$
q^* = \arg\min_q L_q
$$

and then updates the weights as:

$$
W \leftarrow W + \delta W_{q^*}
$$

### OBS Procedure

| Step | Description |
|------|-------------|
| 1 | Choose a network architecture and train it to convergence |
| 2 | Compute the inverse of the Hessian: $H^{-1}$ |
| 3 | Find $q$ that gives the lowest $L_q = \frac{1}{2} \frac{w_q^2}{[H^{-1}]_{qq}}$. If the loss increase is *not too high*, remove the $q^{\text{th}}$ weight. Otherwise, go to step 5. |
| 4 | Using the $q$ from step 3, update all the weights. Then, go to step 3. |
| 5 | Retrain the current network or stop. |

> 💡 Note: If the Hessian is assumed diagonal, we recover the Optimal Brain Damage method. If it's assumed isotropic (a scaled identity matrix), we recover magnitude-based pruning.



## Estimate of $H^{-1}$

Let $F(W, \cdot)$ be the network (assumed here to have a scalar output). The Hessian is approximated as (see section III and IV of the paper for the derivation):

$$
H \approx \alpha I + \frac{1}{P} \sum_{k=1}^{P} \frac{\partial F(W, \text{in}^{[k]})}{\partial W} \cdot \left( \frac{\partial F(W, \text{in}^{[k]})}{\partial W} \right)^T
$$

where:
- $\alpha$ is a small regularization constant
- $\text{in}^{[k]}$ is the $k^{\text{th}}$ input in the training set.

For simplicity of notation, let's rewrite it:

$$
H \approx \alpha I + \frac{1}{P} \sum_{k=1}^{P} X^{[k]} \cdot X^{[k]T}
$$

This expression can be computed iteratively as a recursive sum of a matrix and a rank-one update:

$$H_{m+1} = H_{m} + \frac{1}{P} X^{[m+1]} \cdot X^{[m+1]T}$$

to which we can apply the [Sherman-Morison Formula](https://en.wikipedia.org/wiki/Sherman%E2%80%93Morrison_formula)[2]:

> $$(A + uv^T)^{-1} = A^{-1} - \frac{A^{-1} u v^T A^{-1}}{1 + v^T A^{-1} u}$$

to efficiently get the inverses:

$$
H_{m+1}^{-1} = H_m^{-1} - \frac{H_m^{-1} X^{[m+1]} X^{[m+1]T} H_m^{-1}}{P + X^{[m+1]T} H_m^{-1} X^{[m+1]}}
$$

$$
H^{-1} \approx H_P^{-1}
$$

for a computational complexity of $\mathcal{O}(Pn^2)$ where $n$ is the number of parameters of the network.

This result easily generalizes to the $n_o$-dimensional output case by noting that:

$$H \approx \alpha I + \frac{1}{P} \sum_{k=1}^{P} \sum_{l=1}^{n_0} X^{[k]} \cdot X^{[k]T}$$




## Results
See Section 6 of the paper for experiments.


## References

- [1] LeCun, Yann, John Denker, and Sara Solla. "Optimal brain damage." Advances in neural information processing systems 2 (1989).
- [2] Sherman, Jack, and Winifred J. Morrison. "Adjustment of an inverse matrix corresponding to a change in one element of a given matrix." *Annals of Mathematical Statistics* 21.1 (1950): 124–127.