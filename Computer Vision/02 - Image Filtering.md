# 02 - Image Filtering

## Filters

A filter $h$ takes an input signal $f$ and produces an output signal $g$.

### Linear Shift-Invariant Filter

$$
g = g(f(t), t)
$$
is a *linear filter* if 
$$
g(\alpha f_1 + \beta f_2, t) = \alpha g(f_1) + \beta g(f_2)
$$

and is a *shift-invariant filter* if 
$$
g(t-\Delta t) = g(f(t-\Delta t), t)
$$
i.e. the response of the filter does not depend on $t$.

For linear shift-invariant filters, the output $g$ can be described by the *convolution* of $f$ with the filter's impulse-response *h*.

## Convolution

Convolution of a filter *f* and a filter impulse response *h* is defined as 
$$
g(t) = f(t) * h(t) = \int_{-\infty}^\infty f(\tau)h(t - \tau) d\tau
$$
with discrete form
$$
g[n] = f[n] * h[n] = \sum_{m=-\infty}^\infty f[m]h[n - m]
$$

### Properties

**Commutativity** $f * h = h * f$

**Associativity** $f * (g * h) = (f * g) * h$

**Distributivity** $f * (g + h) = (f * g) + (f * h)$

**Differentiation** $\frac{d}{dx} (f * g) = \frac{df}{dx} * g = f * \frac{dg}{dx}$

### Convolution vs. Cross-correlation

In cross-correlation you do not have to flip the kernel.

### In 2D

$$
f[m, n] * h[m, n] = \sum_{i = -\infty}^\infty \sum_{j = -\infty}^\infty f[i, j]h[m-i, n-j]
$$

To perform a kernel convolution

1. Flip the kernel.
2. Multiply image patch centred at pixel $(m, n)$ with the kernel.
3. Sum over the window of the kernel.

## Moving Average Filter

- Removes high-frequency signals (noise and sharpness). 
- Results in a smooth but blurry image.

Example $3 \times 3$ moving average filter:
$$
\begin{bmatrix}
1 & 1 & 1 \\
1 & 1 & 1 \\
1 & 1 & 1
\end{bmatrix}
\div 9
$$

It is also separable:
$$
\begin{bmatrix}
\frac{1}{9} & \frac{1}{9} & \frac{1}{9} \\
\frac{1}{9} & \frac{1}{9} & \frac{1}{9} \\
\frac{1}{9} & \frac{1}{9} & \frac{1}{9}
\end{bmatrix}
=
\begin{bmatrix}
\frac{1}{3} & \frac{1}{3} & \frac{1}{3} \\
\end{bmatrix}
*
\begin{bmatrix}
\frac{1}{3} \\
\frac{1}{3} \\ 
\frac{1}{3}
\end{bmatrix}
$$

## Edge Handling

**Wrap** - Take values the the opposite edge.

**Constant value** - e.g. zero-padding.

**Mirroring** - e.g. attempting to read a pixel 3 units outside the edge reads one from 3 units inside the edge.

**Crop** - Skip values beyond the edge.

## Computational Complexity

**For a 2D kernel:**

Image size: $N \times N$

Kernel size: $K \times K$

$N^2 K^2$ multiplications and $N^2(K^2-1)$ summations.

Complexity: $O(N^2 K^2)$


**For separable kernels:**

Image size: $N \times N$

Kernel size: $1 \times K$ and $K \times 1$

$2 N^2 K$ multiplications and $2 N^2(K-1)$ summations.

Complexity: $O(N^2 K)$

