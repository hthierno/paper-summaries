#### Main Theorem (simplified)

Let $\sigma$ be a **continuous sigmoidal discriminatory function**. Then the set of functions

$$G(x) = \sum_{j=1}^N \alpha_j \sigma(w_j^T x + \theta_j)$$

is **dense in $C([0,1]^n)$** with respect to the uniform norm.  
That is, for any continuous function $f$ and any $\varepsilon > 0$, there exists such a finite sum $G(x)$ such that:

$$\|f - G\|_\infty < \varepsilon$$

**Proof**

Proceed by **contradiction**:

1. Suppose the set of neural network functions is **not dense** in $C([0,1]^n)$. Let $\mathcal{H}$ be the set of all such finite sums; then its closure is a proper subset: $\overline{\mathcal{H}} \subsetneq C([0,1]^n)$.

2. By the **Hahn–Banach theorem**, there exists a **nonzero continuous linear functional** $L$ on $C([0,1]^n)$ such that:
   $$L(h) = 0 \quad \forall h \in \overline{\mathcal{H}}, \quad \text{but} \quad L(f) \neq 0$$

3. By the **Riesz Representation Theorem**, this functional can be written as:
   $$L(f) = \int_{[0,1]^n} f(x) \, d\mu(x)$$
   for some finite signed Borel measure $\mu$.

4. Since $L(h) = 0$ for all $h \in \mathcal{H}$, it follows that:
   $$\int \sigma(w^T x + \theta) \, d\mu(x) = 0 \quad \forall w, \theta$$

5. But if $\sigma$ is discriminatory, this implies $\mu = 0$, which contradicts $L(f) \neq 0$.

6. Therefore, the assumption is false, and the neural network functions must be **dense** in $C([0,1]^n)$.