# SfM with Uncalibrated Cameras

While can and is used with known intrinsic camera parameters, it's big advantage over other methods is that it can be used with uncalibrated cameras by using the **fundamental matrix** instead of the essential matrix.

## The Fundamental Matrix

So far, we have assumed to know the camera intrinsic parameters and we have used normalized image coordinates to get the epipolar constraint for calibrated cameras:

$$
\begin{bmatrix}
\overline{u}^i_1 \\
\overline{v}^i_1 \\
1
\end{bmatrix} = K_1^{-1} \begin{bmatrix}
u^i_1 \\
v^i_1 \\
1
\end{bmatrix}
\qquad
\begin{bmatrix}
\overline{u}^i_2 \\
\overline{v}^i_2 \\
1
\end{bmatrix} = K_2^{-1} \begin{bmatrix}
u^i_2 \\
v^i_2 \\
1
\end{bmatrix}
$$

$$
\overline{p}_2^T E \overline{p}_1 = 0
$$

$$
\begin{bmatrix}
\overline{u}^i_2 \\
\overline{v}^i_2 \\
1
\end{bmatrix}^T E \begin{bmatrix}
\overline{u}^i_1 \\
\overline{v}^i_1 \\
1
\end{bmatrix} = 0
$$

We can modify the last equation to create the fundamental matrix $F$:

$$
\begin{bmatrix}
\overline{u}^i_2 \\
\overline{v}^i_2 \\
1
\end{bmatrix}^T \underbrace{K_2^{-T} E K_1^{-1}}_{=F} \begin{bmatrix}
\overline{u}^i_1 \\
\overline{v}^i_1 \\
1
\end{bmatrix} = 0
$$

$$
F = K_2^{-T} E K_1^{-1}
$$

## The 8-Point Algorithm for the Fundamental Matrix
The same 8-point algorithm to compute the essential matrix from a set of normalized image coordinates can also be used to determine the fundamental matrix:

$$
\begin{bmatrix}
\overline{u}^i_2 \\
\overline{v}^i_2 \\
1
\end{bmatrix}^T F \begin{bmatrix}
\overline{u}^i_1 \\
\overline{v}^i_1 \\
1
\end{bmatrix} = 0
$$

However, now the key advantage is that **we work directly in pixel coordinates**.

!!! info
    The 8 point algorithm works for both calibrated and uncalibrated cameras, however the 5 point algorithm only works for calibrated cameras.

Similarly to solving the problem with the essential matrix we need to use SVD to solve the following:

$$
\begin{bmatrix}
\bar{u}_2^1 \bar{u}_1^1 & \bar{u}_2^1 \bar{v}_1^1 & \bar{u}_2^1 & \bar{v}_2^1 \bar{u}_1^1 & \bar{v}_2^1 \bar{v}_1^1 & \bar{v}_2^1 & \bar{u}_1^1 & \bar{v}_1^1 & 1 \\\\
\bar{u}_2^2 \bar{u}_1^2 & \bar{u}_2^2 \bar{v}_1^2 & \bar{u}_2^2 & \bar{v}_2^2 \bar{u}_1^2 & \bar{v}_2^2 \bar{v}_1^2 & \bar{v}_2^2 & \bar{u}_1^2 & \bar{v}_1^2 & 1 \\\\
\vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots & \vdots \\\\
\bar{u}_2^n \bar{u}_1^n & \bar{u}_2^n \bar{v}_1^n & \bar{u}_2^n & \bar{v}_2^n \bar{u}_1^n & \bar{v}_2^n \bar{v}_1^n & \bar{v}_2^n & \bar{u}_1^n & \bar{v}_1^n & 1
\end{bmatrix}
\begin{bmatrix}
f_{11} \\
f_{12} \\
f_{13} \\
f_{21} \\
f_{22} \\
f_{23} \\
f_{31} \\
f_{32} \\
f_{33}
\end{bmatrix} = 0
$$

### Problem with 8-Point Algorithm

When using SVD to solve the equation above, we have **poor numerical conditioning**, making our solution **very sensitive to noise**. This can fixed by rescaling the input data, as shown in the example below:

!!! example
    It can be common to have the input data as shown below. The differences in magnitude between the columns is readily visible in the matrix below:

    $$
    \begin{bmatrix}
    250906.36 & 183269.57 & 921.81 & 200931.1 & 146766.13 & 738.21 & 272.19 & 198.81 & 1.00 \\
    2692.28 & 131633.03 & 176.27 & 6196.73 & 302975.59 & 405.71 & 15.27 & 746.79 & 1.00 \\
    416374.23 & 871684.3 & 935.47 & 408110.89 & 854384.92 & 916.9 & 445.1 & 931.81 & 1.00 \\
    191183.6 & 171759.4 & 410.27 & 416435.62 & 374125.9 & 893.65 & 465.99 & 418.65 & 1.00 \\
    48988.86 & 30401.76 & 57.89 & 298604.57 & 185309.58 & 352.87 & 846.22 & 525.15 & 1.00 \\
    164786.04 & 546559.67 & 813.17 & 1998.37 & 6628.15 & 9.86 & 202.65 & 672.14 & 1.00 \\
    116407.01 & 2727.75 & 138.89 & 169941.27 & 3982.21 & 202.77 & 838.12 & 19.64 & 1.00 \\
    135384.58 & 75411.13 & 198.72 & 411350.03 & 229127.78 & 603.79 & 681.28 & 379.48 & 1.00
    \end{bmatrix}
    \begin{bmatrix}
    f_{11} \\
    f_{12} \\
    f_{13} \\
    f_{21} \\
    f_{22} \\
    f_{23} \\
    f_{31} \\
    f_{32} \\
    f_{33}
    \end{bmatrix} = 0
    $$

    Such differences in magnitude means that solving by least-squares (SVD) will yield poor results.

### Normalized 8-Point Algorithm
This can be fixed using a normalized 8-point algorithm [Hartley, 1997][^1], which estimates the Fundamental matrix on a set of **normalized correspondences** (with better numerical properties) and **then un-normalizes the result** to obtain the fundamental matrix for the **given (un-normalized) correspondences**.

**Idea:** Transform image coordinates so that they are in the range $\sim[-1, 1] \times [-1, 1]$.

#### Rescale & Shift Method
The most common method is to apply the rescale and shift method (shown below).

![p536](../img/p536w.svg#only-light)
![p536](../img/p536b.svg#only-dark)

#### Zero-Centroid Method
In the original 1997 paper[^1], Hartley proposed to rescale the two point sets such that the centroid of each set is 0 and the mean standard deviation $\sqrt{2}$ (equivalent to having the points distributed around a circled passing through the four corners of the $[-1, 1]\times[-1, 1]$ square).

This can done for every point as follows:

$$
\hat{p}^i = \frac{\sqrt{2}}{\sigma}(p^i - \mu)
$$

where $\mu = (\mu_x, \mu_y) = \frac{1}{N}\sum_{i=1}^{n}p^i$ is the centroid and $\sigma = \frac{1}{N}\sum_{i=1}^{n}\|p^i - \mu\|^2$ is the mean standard deviation of the point set.

This transformation can be expressed in matrix form using homogeneous coordinates:

$$
\hat{p}^i = \begin{bmatrix}
\frac{\sqrt{2}}{\sigma} & 0 & -\frac{\sqrt{2}}{\sigma}\mu_x \\
0 & \frac{\sqrt{2}}{\sigma} & -\frac{\sqrt{2}}{\sigma}\mu_y \\
0 & 0 & 1
\end{bmatrix}
$$

The normalized 8-point algorithm can therefore be summarized in three steps:

1. **Normalize** the point correspondences: $\hat{p}_1 = B_1 p_1$, $\hat{p}_2 = B_2 p_2$
2. Estimate **normalized** $\hat{F}$ with 8-point algorithm using normalized coordinates $\hat{p}_1$, $\hat{p}_2$
3. Compute **un-normalized** $F$ from $\hat{F}$.

$$
\hat{p}_2^T \hat{F} \hat{p}_1 = 0 \qquad \leftrightarrow \qquad p_2^T b_2^T \hat{F} B_1 p_1 = 0
$$

**Can $R$, $T$, $K_1$ and $K_2$ be extracted from $F$?**

- In general, **no**: infinite solutions exist.
- However, if the coordinates of the principal points of each camera are known and the two cameras have the same focal length $f$ in pixels, then $R$, $T$ and $f$ can determined uniquely.

### Comparison between Normalized and Non-Normalized Algorithm

![p540](../img/p540.png)

|                        | 8-Point | Normalized 8-Point | Non-linear Refinement |
| :--------------------: | :-----: | :----------------: | :-------------------: |
| Avg. Ep. Line Distance | 2.33 px | 0.92 px            | 0.86 px               |

## Error Measures
The quality of the estimated Essential or Fundamental matrix can be measured using different error metrics:

- Algebraic error
- Directional Error
- Epipolar Line Distance
- Reprojection Error

![p541](../img/p541w.svg#only-light)
![p541](../img/p541b.svg#only-dark)

!!! question "When in the error 0?"
    - These errors will be exactly 0 only if $E$ (or $F$) is computed from just 8 points (because in this case a non-overdetermined solution exists).
    - For more than 8 points, it will only be 0 if there is no noise or outliers in the data (if there is image noise or outliers then it the system becomes overdetermined)

### Algebraic Error
This method follows directly from the 8-point algorithm, which seeks to minimize the algebraic error:

$$
err = \|QE\|^2 = \sum_{i=1}^N\left( {\overline{p}^i_2}^T E \overline{p}^i_1 \right)^2
$$

From the proof of the epipolar constraint and using the definition of dot product, it can be observed that:

$$
\| \overline{p}_2^T E \overline{p}_1 \| = \| \overline{p}_2^T \cdot (E \overline{p}_1) \| = \| \overline{p}_2 \| \| E \overline{p}_1 \| \cos(\theta) = \| \overline{p}_2 \| \| [T_\times] R \overline{p}_1 \| \cos(\theta)
$$

We can see that this product depends on the angle $\theta$ between $\overline{p}_2$ and the normal $n = E p_1$ to epipolar plane. It is non-zero when $\overline{p}_1$, $\overline{p}_2$ and $T$ are not coplanar.

!!! question "What is the drawback of this error measure?"
    This method depends on the lengths of $\overline{p}_1$ and $\overline{p}_2$, we only care about the angle between $n$ and $\overline{p}_2$, and this error metric changes based on the lengths of $\overline{p}_1$, $\overline{p}_2$ and the values of $E$.

### Directional Error
This error is defined as the **sum of squared cosines of the angle from the epipolar plane**:

$$
err = \sum_i \left( \cos(\theta_i) \right)^2
$$

It is obtained by **normalizing the algebraic error**:

$$
\cos(\theta) = \frac{\overline{p}_2^T E \overline{p}_1}{\|p_2\| \|E p_1\|}
$$

### Epipolar Line Distance

This method is defined as the **sum of squared epipolar-line-to-point distances**:

$$
err = \sum_{i=1}^N d^2 \left(p_1^i, l_1^i \right) + d^2 \left(p_2^i, l_2^i \right)
$$

This method is faster to compute than the reprojection error because it does not require point triangulation.

![p544](../img/p544w.svg#only-light)
![p544](../img/p544b.svg#only-dark)

### Reprojection Error

This method is defined as the **sum of squared reprojection errors**:

$$
err = \left\| p_1 - \pi(P, K_1, R, T) \right\|^2 + \left\| p_2 - \pi(P, K_2, R, T) \right\|^2
$$

This method is computationally more expensive than the previous three methods because it requires us to first triangulate the 3D points.

However it is the most popular because of its accuracy. The reason is that the error is computed directly with respect to the raw input data, which are image points.

![p545](../img/p545w.svg#only-light)
![p545](../img/p545b.svg#only-dark)

[^1]: [*Richard I. Hartley*, In Defense of the Eight-Point Algorithm](https://www.cse.unr.edu/~bebis/CS485/Handouts/hartley.pdf)