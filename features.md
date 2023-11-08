# Local Features

## Learning Outcomes
- Explain the difference between bag-of-features and feature-detection-based approaches and their applications
- Implement an algorithm for feature detection (Harris corners)
- Explain the desirable properties of feature descriptors

## Approaches to Recognition
### How CNNs Learn from Local Features
- CNNs learn from local features such as:
	- Textures
	- Edges
	- Local shapes
	- Changes in colour 
- This is exhibited with the below images being warped/texturised but still maintaining local features, although it's difficult for us to recognise, a CNN can still recognise

![[local_features_drop.png]]

- An example can be shown below, these features are very important to a CNN
- Although to use they seem insignificant, these are key features that a CNN will look for as they are quite unique for describing the image
- Take note of things like shape, texture, patterns, colours, etc

![[patches.png]]

## Approaches to Recognition
- Detect local features, ignore spatial position
	- Example: bag of words / bag of features
- Local features + weak spatial relations
	- Spatial pyramid models
- Detect local features and model spatial relations between them
	- Deformable-parts models
	- Keypoint tracking / matching

# Recognition from Local Features
## Bag of Words
- A count of the number of occurences of words in a sentence
- All structure between words is lost
- Example is shown below

![[bag_of_words.png]]

## Bag of Features
- We can think of bag of features in the same way
	- Take patches of an image, and remove the spatial relation between them

![[bag_of_features.png]]

- Like bag of words, we would do a histogram and count how many occurrences of a patch, and the highest frequency tells what the image is

![[bag_of_features_histogram.png]]

## Words $\rightarrow$ Features
- Problem: in images, the same "word" can have many appearances

![[same_feature_different_looks.png]]

- Solution: combine similar local features, e.g., with k-means clustering
	- Like what we are doing above

## Histogram Comparison
- As explained above, we can take some patches, form a histogram of the matching patches in an image and use that to match the image

![[patch_histogram.png]]

-  However, these patches are scale variant, and for varying scales for different images, the performance will drop.
	- Therefore we look at other better solutions
 
## Spatial Pyramids
- Main idea: run bag of features at multiple scales
- Note that there's a difference between:
	- Detecting features at one scale and pooling at multiple scales
	- Detecting features at multiple scales

![[smaller_rooms.png]]

- We basically take an image, and we scale it down by 2 so our patches can increase at different scales as we downsample. A little bit like shown below.

![[room1.png]]

![[room2.png]]

![[room3.png]]

## Multiscale Pooling
- Basically split an image into different scales and form a histogram counting the different features per patch
- If split into 4 patches, we have 4 histograms, 8 patches 8 histograms etc.

![[multiscale_pooling.png]]

## Summary
- Bag of words/features = detect local features anywhere in the image, ignore location and spatial relations
- Spatial pyramids add weak spatial relation information to the "bag of features" approach
- Generally works well for category-level recognition - high variance to object translation and pose

# Feature Detection
## Dense vs. Sparse Features
- Dense feature representation: compute local features everywhere
- Sparse feature representation: compute local features only at a few "important" points

![[dense_sparse_features.png]]

## Definitions
- **Feature detection**: finding "important" points (interest points or keypoints) in images
	- What's important?
	- Generally, points that can be detected reliably across image transformations
- **Feature descriptor**: a short code or set of numbers to represent a point in an image

## What Makes a Good Description?
- We have many points in an image, but we want to find the point that is causes the most difference in any direction away from the point.
- An example can be shown in the images below
	- This point is similar in every direction - not a good descriptor
![[descriptor1.png]]

- The below point is better, but sliding along the roof will give a similar point, so still not the best

![[roof_point.png]]

- The below point is different in every direction, so it makes a good descriptor

![[good_descriptor.png]]

## Selecting Good Keypoints
- Should be easy to recognise in a small window
- Shifting the window in any direction should produce a large change in intensity

![[sliding_window_descriptor.png]]

## Corner Detection
- Change in appearance of window $w(x,y)$ for the shift $[u,v]$:

![[corner_detection_error.png]]

Common window functions: square, Gaussian

![[gaussian_kernel.png]]

## Corner Detection
- Change in appearance of window $w(x,y)$ for the shift $[u,v]$:
$$
E(u,v) = \sum_{x,y}w(x,y)[I(x+u,y+v)-I(x,y)]^2
$$
As we can see in the change of window below, the intensity of the colours for each pixel changed when the window shifted to the upper right.

![[window_error.png]]

![[change_in_wondow_error.png]]

- As we can see below, we will have the maximum value on corners, where the biggest change in colour intensities will occur.
- We can also see that for the errors, it is darkest along the edges, thus showing that the error is lowest along the edges. 
- For the corner, there is no line, the darkest point is the corner.

![[errors_along_edges.png]]

## Corner Detection Mathematics
- Approximate shifted intensity using Taylor series:

![[corner_detection_mathematics.png]]

![[simplify_corner_math_formula.png]]

# Values of $M$
- We can see that taking the derivatives of different elements within the matrix along the $x$ and $y$ axis gives Sobel edge detection. 
- We can also see the derivative in both axis gives us the diagonal edges in the image.

![[values_of_m.png]]

## Corner Response Function
- Detect corners using eigenvalues $\lambda_1, \lambda_2$ of $M$
- We can see that for edges, the eigenvalue is large in one axis, but not for the other axis.
- For the corner, the eigenvalues are large for both axes.
- Therefore, the eigenvalues are what defines a corner, find the matrix with large eigenvalues in both axes and you'll find the corner.

![[corner_response_function.png]]

## Corner Response Function

![[corner_response.png]]

- To find corners, look for points where $\lambda_1\lambda_2$ is high, and $\lambda_1 + \lambda_2$ is low

## Harris Corners
- $\lambda_1\lambda_2$ and $\lambda_1 + \lambda_2$ are the determinant and trace of matrix $M$:
	- ``python det = np.linalg.det(m)``    $det(M) = \lambda_1\lambda_2$ 
	- ``python trace = np.trace()``            $tr(M) = \lambda_1 + \lambda_2$ 
- Harris corner response: $R = det(M) - k(tr(m))^2$ 
	- $k$ determined empirically, around 0.04 - 0.06

![[gray_room.png]]

#### Harris corner response = $det(M)-k(tr(M))^2$ 

![[harris_corner_response.png]]

#### Harris Corners

![[red_crosses.png]]

## Invariance / Tolerance
- Corner detection is based on the image gradient (edges), so it's 
	- Invariant to translation
	- Tolerant to changes in lighting
- Because the corner response is based on eigenvalues, it is invariant to image-plane rotation
- Not invariant to scale!

![[scale_variant.png]]

## Summary
- Rather than detecting local features everywhere, feature detectors can be used to find "important" points (interest points or keypoints)
- Common type of interest point = corners
- Corners can be detected from local gradients

# Feature Descriptors
- Having found keypoints in an image, we need a way to represent them
- Options:
	- Image patch
	- Handcrafted descriptors
		- SIFT
		- GLOH
		- BRISK
		- ORB
	- Machine-learned descriptors
		- DeTone, Malisiewicz, & Rabinovich (2018)

![[feature_descriptors_cockatoo.png]]

## Scale-Invariant Feature Transform (SIFT)
- Compute gradient, take histograms in a grid of pixels around interest point
- Weight gradient magnitudes based on distance from centre of patch
- Normalise histograms to sum to 1

![[sift.png]]

## Scale-Invariant Feature Transform (SIFT)
- SIFT implementation details:
	- Patch size = 16 x 16
	- Grid = 4 x 4 cells
	- Histogram bins = 8 orientations
	- Gaussian weighting from centre of patch
	- Two-step normalisation: normalise to sum to 1, truncate values to 0.2, normalise to sum to 1
- Descriptor length = 4 x 4 x 8 = 128

- Interest points (blobs) are detected at multiple scales; descriptor is based on the scale with maximum response
- Histograms are encoded relative to the main orientation in the patch

![[normalised_histogram.png]]

## Summary
- Feature descriptor = a code to represent a local patch or interest point in an image
- Many handcrafted feature descriptors, with different:
	- Encoding method
	- Speed
	- Descriptor size
	- Feature detection method
 - Goal of feature descriptors is invariance, so points can be matched reliably across image transforms
 
 - Most recognition approaches are based on local features, but differ in how they represent spatial relations between features
	 - Bag-of-features methods: no spatial information
	 - Feature detection methods: precise spatial information
	- Choice of approach depends on task
		- Spatial information is probably not needed for category-level recognition
		- Spatial information is useful for tasks that require matching structure across images

# Exercises
## Question 1 - Harris Corner Detection
![[Computer Vision/Images/harris_corner_exercise1.png]]

First let's calculate the $d/dx$:
We perform a convolutional operation with the $d/dx$ kernel over the matrix, and these are the results we get:
$a_{1,1} = -1 \times 1 + 0 \times 0 + 1 \times 5 = 4$
$a_{1,2} = -1 \times 0 + 0 \times 5 + 1 \times 7 = 7$
$a_{1,3} = -1 \times 5 + 0 \times 7 + 1 \times 11 = 6$
$a_{2,1}= -1 \times 1 + 0 \times 4 + 1 \times 9 = 8$

... you get the idea of how to do this by now, so we won't do the rest

$a_{2,2} = 8$
$a_{2,3} = 7$
$a_{3,1} = 8$
$a_{3,2} = 6$
$a_{3,3} = 5$

Now we do the same for the $d/dy$ kernel:
$a_{1,1} = -1 \times 0 + 0 \times 0 + 1 \times 4 = 4$
$a_{1,2} = -1 \times 1 + 0 \times 5 + 1 \times 9 = 8$
$a_{1,3} = -1 \times 4 + 0 \times 7 + 1 \times 12 = 8$

... you should get the idea by now of how we do this

$a_{2,1}= 8$
$a_{2,2} = 6$
$a_{2,3} = 7$
$a_{3,1} = 6$
$a_{3,2} = 6$
$a_{3,3} = 4$

This is the final result:

![[harris_corner_exercise1_ans.png]]

![[harris_corner_exercise2.png]]

$\underset{(x,y) \in W}{\sum} I_x(x,y)^2 = 4^2 + 7^2 + 6^2 + 8^2 + 8^2 + 7^2 + 8^2 + 6^2 + 5^2 = 403$
$\underset{(x,y) \in W}{\sum}I_y(x,y)^2 = 4^2 + 8^2 + 8^2 + 8^2 + 6^2 + 7^2 + 6^2 + 6^2 + 4^2 = 381$
$\underset{(x,y) \in W}{\sum}I_x(x,y)I_y(x,y) = 4 \times 4 + 7 \times 8 + 6 \times 8 + 8 \times 8 + 8 \times 6 + 7 \times 7 + 8 \times 6 + 6 \times 6 + 5 \times 4 = 385$ 
Therefore, when we plug these back into our matrix, we get:

$$
H = \begin{bmatrix}
   403 & 385 \\
   385 & 381
\end{bmatrix}
$$
![[harris_corner_exercise3.png]]

$det(H) = 403 \times 381 - 385 \times 385 = 5318$
$tr(H) = \text{sum of diagonals} = 403 + 381 = 784$
$R = det(H) - K(tr(H))^2 = 5318 - 0.04 \times 784^2 = -19,268.24$

Given that we got a negative value, $\lambda_1 + \lambda_2$ is obviously large compared to $\lambda_1 \lambda_2$. Therefore we can say that this is an edge