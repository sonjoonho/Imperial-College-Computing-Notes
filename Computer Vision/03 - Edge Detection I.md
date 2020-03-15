# 03 - Edge Detection I

**Edge** - lines where image brightness changes sharply and has discontinuities.

## Derivatives

They can occur due to colour discontinuity, depth discontinuity, surface normal discontinuity. An image can be regarded as as *function of pixel position*. **Derivatives** characterise the discontinuities of a function.

## In 1D

For a discrete function, we define **finite difference**.

Central difference: $$f'[x] = \frac{f[x+1]-f[x-1]}{2}$$

Which can be performed using convolution with the kernel 
$$
h=[1, 0, -1]
$$

## In 2D

### Prewitt Operator

Used in edge detection. It is a **discrete differentiation operator**, computing an approximation of the gradient of the image intensity function at each point.

$$
\mathbf{G_x} = 
\begin{bmatrix}
+1 & 0 & -1 \\
+1 & 0 & -1 \\
+1 & 0 & -1 
\end{bmatrix}

\quad

\mathbf{G_y} = 
\begin{bmatrix}
+1 & +1 & +1 \\
0 & 0 & 0 \\
-1 & -1 & -1 
\end{bmatrix}
$$

It is also separable into a (moving average) smoothing filter followed by finite difference.

$$
\begin{bmatrix}
+1 & +1 & +1 \\
0 & 0 & 0 \\
-1 & -1 & -1 
\end{bmatrix}
=
\begin{bmatrix}
1 \\
1 \\
1
\end{bmatrix}
\begin{bmatrix}
+1 & 0 & -1
\end{bmatrix}
$$

### Sobel Operator

Similar to the Prewitt filter.

$$
\mathbf{G_x} = 
\begin{bmatrix}
+1 & 0 & -1 \\
+2 & 0 & -2 \\
+1 & 0 & -1 
\end{bmatrix}

\quad

\mathbf{G_y} = 
\begin{bmatrix}
+1 & +2 & +1 \\
0 & 0 & 0 \\
-1 & -2 & -1 
\end{bmatrix}
$$

It is also separable into a smoothing filter followed by finite difference.

$$
\begin{bmatrix}
+1 & 0 & -1 \\
+2 & 0 & -2 \\
+1 & 0 & -1 
\end{bmatrix}
=
\begin{bmatrix}
1 \\
2 \\
1
\end{bmatrix}
\begin{bmatrix}
+1 & 0 & -1
\end{bmatrix}
$$

## Image Gradient

There are two outputs from Sobel filters: one describing discontinuity along the  *x-axis*, and one describing discontinuity along the *y-axis*. 
$$
g_x = f * h_x
g_y = f * h_y
$$


We can combine these two outputs to describe edges in *two ways*:

1. Magnitude
   $$
   g = \sqrt{g_x^2 + g_y^2}
   $$
   
2. Orientation
   $$
   \theta = \arctan2(g_y, g_x)
   $$

## Smoothing

*Derivatives are sensitive to noise.* Prewitt filters use the mean average kernel $[1, 1, 1]$ for smoothing. Sobel filters use the [1, 2, 1] kernel for smoothing. We can also use the *Gaussian kernel* for smoothing.
$$
\begin{align*}
h(x) &= \frac{1}{\sqrt{2\pi}\sigma}e^{-\frac{x^2}{2\sigma^2}}
\\
h(x, y) &= \frac{1}{2\pi \sigma^2} e^{-\frac{x^2 + y^2}{2\sigma^2}}
\end{align*}
$$

This is separable, and equivalent to the convolution of two 1D Gaussian filters.

## Laplacian

The *Laplacian filter* is the sum of second derivatives and is defined by
$$
\mathbf{G} =
\begin{bmatrix}
0 & 0 & 0 \\
1 & -2 & 1 \\
0 & 0 & 0
\end{bmatrix}
+
\begin{bmatrix}
0 & 1 & 0 \\
0 & -2 & 0 \\
0 & 1 & 0
\end{bmatrix}
=
\begin{bmatrix}
0 & 1 & 0 \\
1 & -4 & 1 \\
0 & 1 & 0
\end{bmatrix}
$$
If we smooth the image using a Gaussian before calculating the Laplacian, which is called the Laplacian of Gaussian filter.