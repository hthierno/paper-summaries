### Derivation of the saliency measure

The goal is to find the set of parameters whose deletion will cause the least increase of the objective function $E$.

Given a pertubation $\delta U = (\delta u_1, \delta u_2, ..., \delta u_n)^T$ of the parameter vector, the change of the objective function $E$ as given by a second-order Taylor expansion of the loss is:

$$\delta E = G^T\delta U + \frac{1}{2} \delta U^T H \delta U = \sum_{i} g_i \delta u_i + \frac{1}{2} \sum_{i} h_{ii} \delta u_i ^2 + \frac{1}{2} \sum_{i \neq j} h_{ij} \delta u_i \delta u_j + O(\lvert \lvert \delta U \rvert \rvert ^3)$$


where $G = (g_1, g_2, ..., g_n)^T$ is the gradient of $E$ with respect to the parameter vector $U$, and $H = (h_{ij})_{ij}$ is the Hessian matrix of $E$ with respect to $U$.

To make the problem computationally feasible, the method makes three approximations:

- **"Diagonal" approximation**
The $\delta E$ caused by deleting several parameter is the sum of the $\delta E's$ caused by deleting each parameters -> H is approximated as diagonal.
- **"Extremal" approxiamtion**
Parameters deletion is performed after the training has converged, so the gradient $G$ is zero, and with the first approximation, this implies that the $h_{ii}'s$ are non-negative.
- **"Quadratic" approximation**
The cost function is nearly quadratic -> a second order Taylor approximation is accurate enough -> $O(\lvert \lvert \delta U \rvert \rvert ^3) \approx 0$


Under these assumptions, the increase in error due to removing a parameter $u_i$ (i.e., setting $\delta u_i = -u_i$) simplifies to:

$$\delta E_i = \frac{1}{2} h_{ii} u_i^2$$

Thus, **saliency** is defined as:

$$\text{saliency}(u_i) = \delta E_i = \frac{1}{2} h_{ii} u_i^2$$