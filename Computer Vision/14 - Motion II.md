# 14 - Motion II

Once we have the motion estimate, what can we use it for?

## Object Tracking

Suppose an object detector provides us with a bounding box for an object. To track the bounding box, we can detect features that are good for tracking e.g. interest points. For each interest point, we apply optic flow methods to estimate motion for each interest point. Then infer the spatial transformation for the bounding box.

Suppose we know that for an interest point $(x_1, y_1)$ at frame $t$ it moves by $(u_1, v_1)$ and reaches $(x_1 + u_1, y_1 + v_1)$ at frame $t + 1$. We know this for a number of points in the bounding box. How do we estimate how much this bounding box moves?

### Approach 1 - Vote

The motion of the bounding box is determined by the median motions of the interest points in the box.

### Approach 2 - Parametric Model

Assume the motion is described by an affine transformation, which can be written as a linear system. Then we solve this using least squares. 

### Tracking in 3D

If we can estimate the **camera matrix**, it is possible to estimate the motion of a 3D object. The camera matrix describes the mapping from 3D coordinates to 2D coordinates in an image.

### Pinhole Camera Matrix

idk

## Action Recognition