# SfM with Calibrated Cameras

In the case where the cameras used are calibrated, we can use the known $K$ matrices to our advantage. For convenience, let's use *normalized image coordinates*:

$$
\begin{bmatrix}
    \bar{u} \\
    \bar{v} \\
    1
\end{bmatrix} = K^{-1}
\begin{bmatrix}
    u \\
    v \\
    1
\end{bmatrix}
$$

With $K$ known, we want to find $R$, $T$ and $P^i$ that satisfy:

$$
\begin{cases} 
\lambda_1 \begin{bmatrix}
\bar{u}_1^i \\
\bar{v}_1^i \\
1 \end{bmatrix} = \begin{bmatrix} I & 0 \end{bmatrix} \begin{bmatrix}
X_w^i \\
Y_w^i \\
Z_w^i \\
1
\end{bmatrix} \\
\lambda_2 \begin{bmatrix}
\bar{u}_2^i \\
\bar{v}_2^i \\
1 \end{bmatrix} = \begin{bmatrix} R & T \end{bmatrix} \begin{bmatrix}
X_w^i \\
Y_w^i \\
Z_w^i \\
1
\end{bmatrix}
\end{cases}
$$

## Scale Ambiguity
If we rescale the entire scene and camera views by a constant factor (i.e. similarity transformation), the projections (in pixels) of the scene points in both images remain exactly the same.

<img 
style="display: block; 
        margin-left: auto;
        margin-right: auto;
        width: 50%;"
src="../../img/p513.png">
</img>

In Structure from Motion, it is therefore not possible to recover the absolute scale of the scene!
- We can do this with stereo vision, because we have prior knowledge of the transformation between cameras (the baseline between the values gives a parameter in physical units).

Thus, only **5 degrees of freedom** are measurable:

- 3 parameters to describe the rotation
- 2 parameters for the translation up to a scale (we can only compute the direction of translation but not its length)

## Knowns & Unknowns
How many knowns and unknowns?

- $4n$ knowns:
    - $n$ correspondences, each one $(u_1^i, v_1^i)$ and $(u_2^i, v_2^i)$ for $i=1\dots n$
- $5+3n$ unknowns:
    - 5 for the motion up to a scale (3 for rotation, 2 for translation)
    - $3n$ = number of coordinates of the $n$ 3D points

Does a solution exist?

- If and only if the number of independent equations ≥ number of unknowns:

    $$
    4n \geq 5+3n \quad\rightarrow\quad n\geq 5
    $$

## Relative Motion Estimation
Can we solve the estimation of relative motion $(R, T)$ independently of the estimation of the 3D points? Yes! This section proves that this is possible.

### The Epipolar Constraint (Recap)
- The camera centers $C_1$, $C_2$ and one image point $p_1$ (or $p_2$) determine the so called **epipolar plane**.
- The intersections of the epipolar plane with the two image planes are called **epipolar lines**.
- **Corresponding points must therefore lie along the epipolar lines**: This constraint is called the epipolar constraint
- And alternative way to formulate the epipolar constraint is to notice that **two correponding image vectors plus the baseline must be coplanar**.

<img 
style="display: block; 
        margin-left: auto;
        margin-right: auto;
        width: 50%;"
src="../../img/p517.png">
</img>

<img 
style="display: block; 
        margin-left: auto;
        margin-right: auto;
        width: 50%;"
src="../../img/p518.png">
</img>

We know that $\bar{p}_1$, $\bar{p}_2$ and $T$ are **coplanar** :

$$
\bar{p}_2^T \cdot n = 0 \rightarrow
\bar{p}_2^T \cdot (T \times \bar{p}'_1) = 0 \rightarrow
\bar{p}_2^T(T \times (R\bar{p}_1)) = 0 \rightarrow
\bar{p}_2^T [T_\times R] \bar{p}_1 = 0
$$

!!! tip
    $\bar{p}'_1$ is the vector $\bar{p}_1$ in the scond camera's frame.

The resulting equation from the deduction above is the **epipolar constraint**:

$$
\bar{p}_2^T E \bar{p}_1^T = 0
$$

The the matrix $E$ is known as the **essential matrix**:

$$
E = [T_\times] R
$$

## How to compute the Essential matrix?

If we don't know $R$, $T$, can we estimate $E$ from two images?

- Yes, given at least 5 correspondences

## The 8-Point Algorithm
Each pair of correspondences $\bar{p}_1 = (\bar{u}_1, \bar{v}_1, 1)^T$, $\bar{p}_2 = (\bar{u}_2, \bar{v}_2, 1)^T$ provides a linear equation:

$$
\bar{p}_2^T E \bar{p}_1 = 0 \quad\text{with}\quad E = \begin{bmatrix}
e_{11} & e_{12} & e_{13} \\
e_{21} & e_{22} & e_{23} \\
e_{31} & e_{32} & e_{33}
\end{bmatrix}
$$

Leading to the equation:

$$
\bar{u}_2 \bar{u}_1 e\_{11} + \bar{u}_2 \bar{v}_1 e\_{12} + \bar{u}_2 e\_{13} + \bar{v}_2 \bar{u}_1 e\_{21} + \bar{v}_2 \bar{v}_1 e\_{22} + \bar{v}_2 e\_{23} + \bar{u}_1 e\_{31} + \bar{v}_1 e\_{32} + e\_{33} = 0
$$

for each point in 3D space seen by the two cameras. For $n$ points, we can write $QE = 0$, which expands to:

$$
\begin{bmatrix}
\bar{u}_2^1 \bar{u}_1^1 & \bar{u}_2^1 \bar{v}_1^1 & \bar{u}_2^1 & \bar{v}_2^1 \bar{u}_1^1 & \bar{v}_2^1 \bar{v}_1^1 & \bar{v}_2^1 & \bar{u}_1^1 & \bar{v}_1^1 & 1 \\\\
\bar{u}_2^2 \bar{u}_1^2 & \bar{u}_2^2 \bar{v}_1^2 & \bar{u}_2^2 & \bar{v}_2^2 \bar{u}_1^2 & \bar{v}_2^2 \bar{v}_1^2 & \bar{v}_2^2 & \bar{u}_1^2 & \bar{v}_1^2 & 1 \\\\
\vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots \\\\
\bar{u}_2^n \bar{u}_1^n & \bar{u}_2^n \bar{v}_1^n & \bar{u}_2^n & \bar{v}_2^n \bar{u}_1^n & \bar{v}_2^n \bar{v}_1^n & \bar{v}_2^n & \bar{u}_1^n & \bar{v}_1^n & 1
\end{bmatrix}
\begin{bmatrix}
e_{11} \\
e_{12} \\
e_{13} \\
e_{21} \\
e_{22} \\
e_{23} \\
e_{31} \\
e_{32} \\
e_{33}
\end{bmatrix} = 0
$$

The matrix $Q$ is known, whereas the matrix $E$ is unknown.

### Solving the 8-Point Algorithm
**Minimal Solution**

- $Q_{n\times 9}$ should have rank 8 to have a unique (up to scale) non-trivial solution $\bar{E}$
- Each point correspondence provides 1 independent equation
- Thus, 8 point correspondences are needed

**Over-Determined Solution**

- $n>8$ points
- A solution is to minimize $\|Q\bar{E}\|^2$ subject to the constraint $\|\bar{E}\|^2 = 1$
    The solution is the eigenvector corresponding to the smallest eigenvalue of the matrix $Q^T Q$ (because it is the unit vector $x$ that minimizes $\|Qx\|^2 = x^T Q^T Q x$).
- It can be solved through singular value decomposition (SVD)

**Degenerate Configurations**

- The solution of the 8-point algorithm is **degenerate when the 3D points are coplanar**.
- Conversely, the 5-point algorithm works also for coplanar points.

## Extract $R$ and $T$ from $E$
Singular value decomposition yields the following: $E = U \Sigma V^T$.

Enforcing a rank-2 constraint: We set the smallest singular value of $\Sigma$ to 0:

$$
\Sigma = \begin{bmatrix}
    \sigma_1 & 0 & 0 \\
    0 & \sigma_2 & 0 \\
    0 & 0 & \sigma_3
    \end{bmatrix} = \begin{bmatrix}
    \sigma_1 & 0 & 0 \\
    0 & \sigma_2 & 0 \\
    0 & 0 & 0
    \end{bmatrix}
$$

$$
\hat{T} = U \begin{bmatrix}
    0 & \mp 1 & 0 \\
    \pm 1 & 0 & 0 \\
    0 & 0 & 0
    \end{bmatrix} \Sigma V^T \quad \text{where} \quad \hat{T} = \begin{bmatrix}
    0 & -t_z & t_y \\
    t_z & 0 & t_x \\
    -t_y & t_x & 0
    \end{bmatrix} \rightarrow \hat{t} = \begin{bmatrix} t_x \\ t_y \\ t_z \end{bmatrix}
$$

$$
\hat{R} = U \begin{bmatrix}
    0 & \mp 1 & 0 \\
    \pm 1 & 0 & 0 \\
    0 & 0 & 0
    \end{bmatrix} V^T
$$

Therefore $R$ and $T$ can be found as follows:

$$
\begin{align}
    T &= K_2 \hat{t} \\
    R &= K_2 \hat{R} K_1^{-1}
\end{align}
$$

Such an answer gives four possible solutions. However, thereis only one solution where the points are in front of both cameras:

<img 
style="display: block; 
        margin-left: auto;
        margin-right: auto;
        width: 70%;"
src="../../img/p528.png">
</img>
