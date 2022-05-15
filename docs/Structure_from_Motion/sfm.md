# Structure from Motion (SfM)

## Problem Formulation
Given a set of $n$ point correspondences between two images, $p_1^i = (u_1^i, v_1^i)$, $p_2^i = (u_2^i, v_2^i)$, where $i=1 \dots n$, the goal is to simultaneously:

- Estimate the 3D points $P^i$
- The camera relative-motion parameters $(R, T)$
- The camera intrinsics $K_1$, $K_2$ that satisfy:

    $$
    \begin{cases} 
    \lambda_1 \begin{bmatrix}
    u_1^i \\
    v_1^i \\
    1 \end{bmatrix} = K_1 \begin{bmatrix} I & 0 \end{bmatrix} \begin{bmatrix}
    X_w^i \\
    Y_w^i \\
    Z_w^i \\
    1
    \end{bmatrix} \\
    \lambda_2 \begin{bmatrix}
    u_2^i \\
    v_2^i \\
    1 \end{bmatrix} = K_2 \begin{bmatrix} R & T \end{bmatrix} \begin{bmatrix}
    X_w^i \\
    Y_w^i \\
    Z_w^i \\
    1
    \end{bmatrix}
    \end{cases}
    $$

    <img 
    style="display: block; 
           margin-left: auto;
           margin-right: auto;
           width: 50%;"
    src="../../img/p510.png">
    </img>

## Two Problem Variants
Two variants of this problem exist:

- **Calibrated** camera(s) $\rightarrow K_1$, $K_2$ are **known**
- **Uncalibrated** camera(s) $\rightarrow K_1$, $K_2$ are **unknown**