# Edges

## Learning Outcomes
- Explain the causes of edges in images
- Explain the steps involved in Canny edge detection
- Evaluate computer vision features in terms of their invariance (or tolerance) to image variation

# Edge Detection

## Causes of Edges
- Edges are caused by a variety of factors:

![[causes_of_edges.png]]

![[shadows_and_edges.png]]

## Characterising Edges

![[Computer Vision/Images/characterising_edges.png]]

![[characterising_edges1.png]]

## Gradient

![[gradient.png]]

-  Gradient of a function over $x$, $y$:
- $\nabla f = \frac{df}{dx} i + \frac{df}{dy} j$
	- $i$ = unit vector in the $x$ direction
	- $j$ = unit vector in the $y$ direction
- Gradient at a single point ($x$, $y$) is a vector:
	- Direction is the direction of maximum slope:
	- $\theta = \tan^{-1}(\frac{df}{dx}/\frac{df}{dx})$ 
	- Length is the magnitude (steepness) of the slope
	- $\lVert \nabla f \rVert = \sqrt{(\frac{df}{dx})^2+(\frac{df}{dy})^2}$

## Partial Derivatives in $x$, $y$

![[partial_derivative_in_xy.png]]

## Issue: Noise
- Consider a single row or column of the image
	- Plotting intensity as a function of position gives a signal

![[issue_noise.png]]

## Solution: Smooth (Blur) First

![[solution_smooth_first.png]]

## More Efficient Solution
- Associative property of convolution: $\frac{d}{dx}(f * g) = f * \frac{d}{dx}g$ 

![[more_efficient_solution.png]]

## Edge Filter: Sobel

![[common_filters_sobel.png]]

![[common_filters_sobel2.png]]

## Edge Filters

![[edge_filters.png]]

# Example: Canny Edge Detection

## Canny Edge Detection
- Canny, _TPAMI 1986_: A Computational Approach to Edge Detection
- Foundational approach to edge detection
- Detect edges based on image gradient, then do additional processing to improve the edge map

## Edge Detection
- Filter with derivative-of-Gaussian filters
- Get magnitude, orientation of all the edges
- You really only need two oriented filters ($dx$ and $dy$)

$$
\frac{d}{d\theta} = \cos (\theta) \frac{d}{dx} + \sin (\theta) \frac{d}{dy}
$$

![[shadows_and_edges.png]]

![[derivative-in-x-canny.png]]

![[derivative-in-y-canny.png]]

![[magnitude-of-gradient-canny.png]]

## Problem: Multiple Edges
- How many vertical edges?

![[problem_mutiple_edges.png]]

- Non-maximum suppression: If nearby pixels claim to be part of the same edge, only keep the one with maximum gradient

## Non-Maximum Suppression
- Bin edges by orientation
- For each edge pixel,
	- Check the two pixel neighbour pixels orthogonal to this edge pixel
	- If either neighbour has same edge orientation AND higher magnitude, this pixel is not an edge

![[non_maximum_suppression.png]]

![[after_non_max_suppression.png]]

![[edge_orientation.png]]

## Thresholding with Hysteresis
- Magnitude threshold?
- No single threshold will work: use hysteresis

![[thresholding_with_hysteresis.png]]

- Two thesholds $T_1$, $T_2$ with $T_1 > T_2$
- Strong edges: magnitude > $T_1$
- Weak edges: $T_1$ > magnitude > $T_2$
- For each weak edge:
	- Check the 8-pixel neighbourhood around this pixel
	- If any neighbour is a strong edge, relabel the weak edge pixel as a strong edge
- Final edge map = strong edges

![[after_thresholding-canny.png]]

# Edges for Image Recognition

## Edge Features in Neural Networks

![[edge_features_in_neural_networks.png]]

## Why Edges?

![[why_edges.png]]

- Compression
	- Edge = discontinuity
	- Efficient way to represent images: only represent points where the signal changes (e.g., Elder & Zucker, 1996)
- Invariance
	- Edge-based features are invariant or tolerant to many irrelevant image changes

## Definitions
- **Invariant** to $X$ = response/representation does not vary with $X$, is insensitive to changes in $X$
- **Tolerant** to $X$ = response is mostly insensitive to $X$

![[definitions-invariant-tolerant.png]]

## Invariant to Light Intensity?
- Image derivative is invariant to intensity shift ($I_{new} = I + b$)
- Tolerant to contrast change ($I_{new} = aI$), but depends on thresholds

![[invariant_to_light_intensity.png]]

## Invariant to Light Direction?

![[invariant_to_light_direction.png]]

## Invariant to Translation?

![[invariant_to_translation.png]]

## Invariant to Rotation?

![[invariant_to_rotation.png]]

## Invariant to Scale?

![[invariant_to_scale.png]]

## Invariant to 3D Rotation / Pose?

![[invariant_to3d_rotation.png]]

## Image Recognition
- To recognise objects across variations in lighting, position, size, pose, etc.:
	- Learn invariant features and compare them to image
	- Learn a separate set of features for each variation (e.g., 8 different rotations) and compare each one to image
- Recognition algorithms often use a mixture of both strategies