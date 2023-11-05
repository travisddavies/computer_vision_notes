# Edges

## Learning Outcomes
- Explain the causes of edges in images
- Explain the steps involved in Canny edge detection
- Evaluate computer vision features in terms of their invariance (or tolerance) to image variation

# Edge Detection

## Causes of Edges
- Edges are caused by a variety of factors:
	- **Surface normal discontinuity**: Anywhere where there's a change in the surface normal. i.e. a corner or a rounded edge (like shown in image)
	- **Depth discountinuity**: A change in surface entirely. Basically means you've got completely different materials, normals and reflectances
	- **Surface colour discountinuity**: Different patterns creating different reflectances on the surface. An example is the writing in the below image causing different reflectances to the background
	- **Illumination discontinuity**: Strong images creating a change in illumination. An example is an object casting a shadow. This has nothing to do with the actual change with the 3D surface or reflectance. This is considered the most annoying edge because it has no correlation with anything


![[causes_of_edges.png]]

Some examples may include the following for the below image:
- The centre pillar turning the corner would be considered a **surface normal continuity**
- The shadow cast by the pillar would be considered an **illumination discontinuity**
- The shadow on the dome is not really considered an edge since it is so gradual - but depends on scale in focus

![[shadows_and_edges.png]]

## Characterising Edges
- We can consider edges where we see a change in intensity
- For the slice below, we can see noticeable changes in height. The areas with significant change in height are considered edges

![[Computer Vision/Images/characterising_edges.png]]

- The best way to look for edges is to look for the peak and trough of the derivative for the intensity function. As shown below.

![[characterising_edges1.png]]

## Gradient
- This is just an example of showing the gradients of an image, where the arrows are pointing in the direction of the gradient

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
- We can also find the partial derivative in the $x$ and $y$ direction, as shown in the two images below.
- Note that the $\frac{df}{dx}$ gives the vertical edges, whereas $\frac{df}{dy}$ gives the horizontal edges 

![[partial_derivative_in_xy.png]]

## Issue: Noise
- Consider a single row or column of the image
	- Plotting intensity as a function of position gives a signal
 - What this means is that since images are noisy, when we take the derivative of an image we will get something very messy looking like shown below.
 - In the $f(x)$ function it is clear where the edge lies, but for the derivative it is less clear where the edge is.
 - We therefore need to use other methods to solve this issue

![[issue_noise.png]]

## Solution: Smooth (Blur) First
- When we apply a Gaussian blue to the signal, it smooths out the noise and becomes much clearer where the peak is in the first derivative (shown on bottom image)

![[solution_smooth_first.png]]

## More Efficient Solution
- Associative property of convolution: $\frac{d}{dx}(f * g) = f * \frac{d}{dx}g$ 
- Remember what we discussed in [spatial filters](spatial_filtering.md)!

![[more_efficient_solution.png]]

## Edge Filter: Sobel
- As mentioned in [spatial filters](spatial_filtering.md), the Sobel filter is the derivative of the Gaussian filter

![[common_filters_sobel.png]]

![[common_filters_sobel2.png]]

## Edge Filters
- This is a visual example of the Sobel filter

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
	- If we were a computer, we would say that there's about 35 different edges since they all have non-zero values for the gradients
	- However we would define this as one edge
- Therefore we need to accommodate for this problem in the Canny Edge detector
- **Non-maximum suppression**: If nearby pixels claim to be part of the same edge, only keep the one with maximum gradient

![[problem_mutiple_edges.png]]

## Non-Maximum Suppression
- Bin edges by orientation - normally 4 bins: vertical, horizontal and 2 diagonals
- For each edge pixel,
	- Check the two neighbour pixels orthogonal to this edge pixel
	- If either neighbour has same edge orientation AND higher magnitude, this pixel is not an edge
 - Take note of the image below, for the first image we would look at either white pixel to the left and right of the red line and see if they have a higher magnitude and same edge orientation as the centre pixel. We would continue this for each image.
 - This the solved the multiple edge problem shown above

![[non_maximum_suppression.png]]

- The result:

![[after_non_max_suppression.png]]

![[edge_orientation.png]]

## Thresholding with Hysteresis
- Magnitude threshold?
- How do we gauge what a true, strong edge is in an image where we have many weak images? There's no set solution for this!
- We would like to throw away these low contrast edges, but we don't want to throw away the true edges in the image
- No single threshold will work: use hysteresis

![[thresholding_with_hysteresis.png]]

- Two thresholds $T_1$, $T_2$ with $T_1 > T_2$
- Strong edges: magnitude > $T_1$
- Weak edges: $T_1$ > magnitude > $T_2$
- For each weak edge:
	- Check the 8-pixel neighbourhood around this pixel
	- If any neighbour is a strong edge, relabel the weak edge pixel as a strong edge
- Final edge map = strong edges
- As can be seen in the image below, lots of weak edges have now been removed from our output

![[after_thresholding-canny.png]]

## Canny Edge Detector: General Process
1. Denoise the image with Gaussian filter
2. Calculate the gradients of the image
3. Apply non-max suppression on the edges of the image

# Edges for Image Recognition

## Edge Features in Neural Networks
- Edges are the basis for every artificial visual systems
- Neural networks in the very early layers are actually also creating edge detectors in their kernels, showing their importance in for understanding images for visual learning tasks

![[edge_features_in_neural_networks.png]]

## Why Edges?
- This is because us humans also rely on edges in our visual system.
	- Below shows a study of neural systems being very responsive to images when given a visual cue.

![[why_edges.png]]

- Compression
	- Edge = discontinuity
	- Efficient way to represent images: only represent points where the signal changes (e.g., Elder & Zucker, 1996)
- Invariance
	- Edge-based features are invariant or tolerant to many irrelevant image changes

## Definitions
- **Invariant** to $X$ = response/representation does not vary with $X$, is insensitive to changes in $X$
- **Tolerant** to $X$ = response is mostly insensitive to $X$
- Below shows the difference in response when we change the scale of the left image. Our aim is for the response to be the same for all scales to make it tolerant to scale

![[definitions-invariant-tolerant.png]]

## Invariant to Light Intensity?
- Image derivative is invariant to intensity shift ($I_{new} = I + b$)
- Tolerant to contrast change ($I_{new} = aI$), but depends on thresholds
- But in general, yes it is considered invariant to light intensity!

![[invariant_to_light_intensity.png]]

## Invariant to Light Direction?
- As shown below, edges are generally invariant to light direction
- This is because it is looking for changes in surface normal/surface don't change regardless of the light direction.

![[invariant_to_light_direction.png]]

![[invariant_to_light_direction2.png]]
## Invariant to Translation?
- Edges are invariant to translation
- Even though we have translated these two images, the gradients remain the same

![[invariant_to_translation.png]]

![[invariant_to_translation2.png]]
## Invariant to Rotation?
- Edges are invariant to rotation
- Much like the translation invariance, the gradients did not change when rotated
- However, one thing to keep in mind, the orientation of the edges has now changed, so technically not the original edges anymore
- One strategy we could use to make it rotation invariant is to make 100 different combinations of edges for the rotated image, and then find the rotated image that matches the second image

![[invariant_to_rotation.png]]

![[invariant_to_rotation2.png]]
## Invariant to Scale?
- Edges are not actually invariant to scale
- Take a look at the image below, the edge within the box is no longer the same and is therefore edges are invariant to changing sizes

![[invariant_to_scale.png]]

## Invariant to 3D Rotation / Pose?
- Edges are not invariant to 3D poses.
- This is obviously because these edges are no longer the same, however if we take various samples of the edges, we can finding matching features between the two images

![[invariant_to3d_rotation.png]]

![[invariant_to_3D_rotation.png]]
## Image Recognition
- To recognise objects across variations in lighting, position, size, pose, etc.:
	- Learn invariant features and compare them to image
	- Learn a separate set of features for each variation (e.g., 8 different rotations) and compare each one to image
- Recognition algorithms often use a mixture of both strategies