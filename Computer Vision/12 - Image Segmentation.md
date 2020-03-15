# 12 - Image Segmentation

**Image segmentation** is the process of partitioning an image into multiple regions, each region consisting of pixels with similar properties, or semantics.

This is a similar task to object detection, and they differ only in the level of detail.

## Unsupervised Techniques

### Thresholding

This is the simplest method for segmentation. It converts a grayscale image into a binary label map. At each pixel $x$, the label is defined by
$$
f(x) =
\begin{cases}
1 && I(x) \geq \text{threshold} \\
0 && \text{otherwise}
\end{cases}
$$
No training data is involved. The only parameter is the threshold. It assumes that pixel intensities can be grouped into two clusters, and a threshold can separate the two.

### K-means clustering

However, in general there can be more than two clusters. We represent each cluster by its centre. Each data point is associated to the nearest cluster centre. The *optimal cluster centres* are those that minimise the **intra-class variance**, defined as the sum of squared difference between each data point and its associated cluster centre.
$$
\min \sum_{k=1}^K \sum_x \delta_{x,k} (x-\mu_k)^2
$$
where $\delta_{x,k}$ denotes whether $x$ is assigned to cluster $k$.

### Gaussian Mixture Models

GMM performs soft assignment by assuming a Gaussian distribution for each cluster. The probability that $x_j$ belongs to a cluster $k$ is
$$
P(y_j = k | x_j,\pi_k,\mu_k,\sigma_k) = \pi_k \cdot \frac{1}{\sqrt{2\pi}\sigma_k} \exp\left( - \frac{(x-\mu-k)^2}{2\sigma_k^2} \right)
$$
where $x_j$ is the intensity for data point $j$, $j_j$ is the class for data point $j$, $\pi_k$ is the mixing component for class $k$.

1. Initialise the parameters $\pi_k, \mu_k, \sigma_k, k = 1,2...,K$.

2. For each iteration 

   1. Compute the expected membership for each data point $x_j, j = 1,...,N$.

   2. Update model parameters
      $$
      \begin{align*}
      \pi_k &= \frac{\sum_j P(y_j=k)}{N} \\
      \mu_k &= \frac{\sum_j P(y_j=k) \cdot x}{\sum_j P(y_j = j)} \\
      \sigma_k^2 &= \frac{\sum_x P(y_j = k) \cdot (x - \mu_k)^2}{\sum_x P(y_j = k)}
      \end{align*}
      $$

3. Repeat until convergence.

Once we know the probability of a data point $x_j$ belonging to class $c$, we can perform image segmentation. We can either assign a class to each pixel based on maximum probability, or add some smoothness to the segmentation.

## Supervised Techniques

### Fully Convolutional Networks

We can use the same techniques with a sliding window in object detection, but apply classification to each pixel. However, it is expensive to apply to network to so many pixels. 

In **fully convolutional network**, every layer is convolutional. They replace fully-connected layers, and in this way we preserve spatial information and hence can output a probability map that gives a probability for *each pixel* in the image. The output of this image will be coarse (low resolution) so we need to upsample it to the same size as the original image. To upsample, we can use **transposed convolution**.

#### Mask-CNN

Previously we used Faster R-CNN for object detection.

> Mask R-CNN = Faster R-CNN + segmentation branch

The input is the pooled feature map, fed into three branches

1. Classification
2. Localisation
3. Segmentation

The segmentation branch is fully convolutional.