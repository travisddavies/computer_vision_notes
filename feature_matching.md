# Feature Matching
- Feature matching is basically the process of finding features of something in an image to be able to match with the same thing in another image at any angle in 3D pose
- Example shown below

![[feature_matching.png]]

- Goal:
	- Simultaneously recognise object and its pose in 3D
	- Track features across multiple views
- Challenges
	- Features will have different appearances in each view (due to changes in pose, lighting, etc.)
	- Some features visible in one view may be missing in the other
	- Background could contain similar "matching" features

## Feature Matching as Model Fitting
- Not just looking for same features, but same spatial arrangement of features
- Model-fitting problem:
	- Propose a model that explains correspondence between matched points
	- Find points that fit model / measure goodness of fit / find model parameters
- Problems:
	- Outliers (data not explained by model)
	- Noise in data that is explained by model

## Hough Transform
- For something such as edge detection, we often can't simply extract the lines from the edge detection results, as we will have broken lines and various textures that make the process very complicated to extract the naive means.
- Therefore, we need a new approach to extracting the edges, such as the Hough Transform

![[gray_room.png]]

![[canny_edge_map.png]]

## Hough Transform
- Each edge point "votes" for the lines that pass through that point
- Identify lines with the most "votes"
- We can think of this as we will take random lines in the parameter space that intersect with the dots on the left graph, and eventually we will find a point in the parameter space that intersects with all the points on the left graph

![[hough_transform.png]]

- Identifying the parameters with most votes is easy if there is no noise in point locations
- Usually there is some noise

![[line_voting.png]]

- Solution to noise: bin parameters
- Points vote for bins

![[bin_hough_transform.png]]

- Another problem: slope and intercept ($m,b$) are unbounded
- Solution: use a polar representation of ($m,b$)

![[hough_transform_sin.png]]

## Example: Hough Transform
Pay attention to how the Hough transform lines change as the points on the left change.

The first image has the $m$ and $b$ as reasonably constant, so the point on the right is very concentrated in one point

![[example_hough_transform.png]]

The second one is a bit more noisy, so as we can see the Hough Transform concentrated point is now not so concentrated

![[example_hough_transform2.png]]

Now we have multiple concentrated lines of points, we can see that it forms multiple concentrated points in the Hough transform

![[example_hough_transform3.png]]

## Hough Transform Parameters
- Bin size
- Threshold for peaks
- Number of peaks (= number of lines)
- Minimum line/segment length
- Maximum allowed gap (to treat segments as the same line)

## Looking at a Real World Example

![[canny_edge_detector.png]]

### Hough Transform Peaks
We can see that an image like this is more complicated to find the Hough transform peaks, but as shown by the red squares on the right we can still find them

![[canny_room_hough_transform.png]]

And with a little bit of tinkering, we can get out the edges image with the Hough Transform

![[hough_lines.png]]

## Hough Transform Efficiency
- Hough transform is a grid search for parameters
- What happens as $N$ parameters increase?
	- Basically since we need to perform a grid search for every possible combination in the search space, and $N$ represents the number of dimensions in the search space, increasing $N$ means that we would have to search for every combination in the $N$-dimensional search space.
	- Therefore, this algorithm becomes very unwieldy as we increase the number of parameters.

![[example_hough_transform.png]]

## Summary
- Hough transform is a method for detecting structure in images
- Each pixel "votes" for parameters
- Common application:
	- Line detection
	- Circle detection
- Basically grid search over all possible values of each parameter, so limited to just a few parameters

# RANSAC
- RANSAC = RANdom Sample Consensus
- Like Hough transform, points "vote" for the model that explains those points
- Iteratively:
	- Sample $N$ points at random (when $N$ is the number of points needed to fit the model)
	- Calculate model parameters
	- Count the number of points that are explained by the model (inlier points)
- Best model = model that explains the most points

## Example: RANSAC Line Fitting

![[ransac_example.png]]

![[ransac_step1.png]]

![[ransac_step2.png]]

![[ransac_step3.png]]

![[ransac_final.png]]

## RANSAC Parameters
- Number of iterations
	- Should be high enough that it is very likely (e.g., prob=0.99) that you will obtain at least one sample with no outliers
	- Example: Half of your data is outliers, and you want to fit a line. How many samples?
		- Probability of getting a good point for line fitting is 0.5. Taking two good points is therefore $0.5 \times 0.5 = 0.25$  
		- Using Bernoulli trials of 16 and a probability of having two good points of 0.25, we can see that there is 0.99 chance that we will get at least one good point in our line since it is 0.01 chance we get no good points.
 
		![[odf.png]]
- Threshold for inliers
	- Choose $\delta$ so that a good point with noise is likely (e.g., prob=0.95) within threshold

## Application: Instance Recognition
- Does the second image contain the same features in the _same configuration_?
- We can see that features are much like the RANSAC problem, and can be fit to objects in an image in 3D pose

![[feature_matching.png]]

## Feature Matching
Step 1. Find keypoints + descriptors in each image

![[step1_feature_matching.png]]

Step 2. Find candidate matches: for each keypoint in image 1, find most similar match in image 2

![[step2_feature_matching.png]]

Problems:
- Many keypoints, many similar features - false matches are likely
- For more robust matching - consider ratio (similarity to best match / similarity to second best)

![[step3_feature_matching.png]]

## Reducing False Matches
- What we find is that as the likelihood of correct matches being high and incorrect matches being low, the correct match of features is less likely.
- Same for the opposite case
- The best case when the probability of incorrect and correct matches being about equal is the best ratio for correctly feature matching

![[correct_false_matches.png]]

## Feature Matching
- Exhaustive matching is very slow ($O(m n)$), $m,n$=number of keypoints in each image 
- More efficient option: approximate nearest neighbours
	- Faster, but may not find the closest match
- Even with ratio matching, proportion of false matches is likely to be very high

Step 3. Use RANSAC to find subset of matches that can be explained by a transformation model

## What Model?
- What we see in the below pictures is that the actual building inside the image is the same, but the actual angle of the building to the camera is different.
- We can think of this as a transformation

![[what_model.png]]

How can points on a flat surface transform between two camera views?

![[possible_transformation.png]]

## Affine Transformations
- Affine transformation is any combination of translation, scale, rotation, and shear
- Examples are shown below:

![[affine_transformation.png]]

## Projective Transformation
- Projective transformation combines affine transformation with a projective warp
$$
\begin{bmatrix} 
   x' \\  
   y' \\
   w'  
\end{bmatrix} =
\underset{\text{Projective}}{\begin{bmatrix}
   a & b & c \\
   d & e & f \\
   g & h & i 
\end{bmatrix}}
\begin{bmatrix}
   x \\
   y \\
   w
\end{bmatrix}
$$

![[projective_transformation.png]]

## 2D Planar Transformations

![[2d_planar_transformations.png]]

## How Many Points?
- How many points do you need to define a projective transform?
- Projective transform can include any combination of scaling, rotation, affine shift, and projective warp

![[how_many_points.png]]

One point is insufficient to recognise any transform

![[one_points.png]]

Two points - can't distinguish between different combinations of scaling, shift, and warp

![[two_points.png]]

Three points - sufficient to recognise an affine-only transform, but can't distinguish between an affine-only and projective transforms

![[three_pointa.png]]

Four points - unique solution!

![[four_points.png]]

## Feature Matching with RANSAC 
1. Randomly sample the number of points needed to fit model (e.g., 4 of projective)
2. Compute model parameters from points
3. Count inlier points

Repeat until max iterations, take model with most inliers

![[ransac_feature_matching.png]]

## Summary
- Features matching with RANSAC finds matching keypoints across images
- Finds points across views, and computes the transformation that relates those points
- Applications:
	- Instance recognition
	- Video stabilisation
	- Panorama stabilisation
	- Finding planes, vanishing points, etc. in images
	- 3D reconstruction