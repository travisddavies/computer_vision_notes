# Multi-View Problem
## Standard Stereo Set-Up
- Assume:
	- Image planes of cameras are parallel to each other and to the baseline $B$
	- Camera centres are at the same height
	- Focal lengths $f$ are the same
- Goal: find $z$

![[depth_from_stereo 1.png]]

## Depth from Multiple Views
- What if you don't know the change in camera position between the views?
- What if the camera parameters (focal length, etc.) are unknown?

## Multi-View Problem
- Solve for:
	- Camera motion - what is the transform (camera translation + rotation) that relates the two views?
	- Camera parameters (e.g., focal length), if not known
	- Scene geometry - given corresponding image points ($x, x'$) in the two views, what is the position of the point $X$ in 3D space?

## Camera Calibration
- We know how to compute 3D ($x,y,z$) from image plane($x,y$) when the imaging parameters are known
- Usually, these parameters are unknown

![[camera_calibration.png]]

## Camera Parameters
- **Intrinsic parameters**: camera parameters related to image formation (focal length, optical centre, lens distortion)
- **Extrinsic parameters**: camera pose (location and orientation) relative to the world
- Camera calibration is a process to find the intrinsic parameters
- Usually, these parameters are learned from image data with unknown extrinsic parameters

## Homogeneous Coordinates
- When converting between world and image points, it is often convenient to use **homogeneous** (or **projective**) coordinates
- Image points are represented with 3 values ($x_i,y_i,z_i$)
- The third value can be thought of as the distance to the image plane

![[homogeneous_coords.png]]

## Projection Model
- The pinhole projection model can be represented as a matrix in homogeneous coordinates:

![[Computer Vision/Images/projection_model.png]]

$$
(f_x, f_y) = \text{focal length} \ \ \ \ \ (c_x, c_y) = \text{optical centre}
$$
- We can think of this middle matrix as the intrinsic parameters of the camera. Thus if we know these values in the middle matrix we can find the image coordinates and the real world coordinates.
	- The left hand equations demonstrate how we would do this

## Camera Calibration Method
- Camera calibration requires a calibration target, a planar surface with a known pattern that is easily detected/tracked by feature detection methods
	- Common choices: checkerboard, squares, circles
- Take multiple photos (or a video) of the calibration target in many different poses
- Solve for intrinsic and extrinsic parameters

## Calibration Target

![[calibration_target.png]]

## Projection Model
- Relationship between points in the world and points in the image:

![[projection_model 1.png]]

$$
(f_x, f_y) = \text{focal length} \ \ \ \ \ (c_x, c_y) = \text{optical centre}
$$
- Coordinates in the image will vary for each keypoint in an image
- Camera matrix will remain the same for each scene/camera
- Rotation + rotation matrix will vary for each image taken
	- This will be a 4x4 matrix to apply against the camera matrix
- Real world coordinates vector varies for various objects in the world

- For simplicity, assume the calibration target is aligned with the plane $z=0$, i.e., the third row in the real world coordinates vector becomes zero.

$$
\begin{bmatrix} 
   x_i \\  
   y_i \\
   1  
\end{bmatrix} = H
{\begin{bmatrix}
   x \\
   y \\
   0 \\
   1
\end{bmatrix}}
$$
- $H$ we can think of as the combination of the camera matrix and the rotation + translation matrix
## Camera Calibration Algorithm 
- Given multiple images, can solve for $H$, and camera matrix using a system of linear equations
- Note that this model assumes no lens distortion
- Given best fit for $H$, estimate distortion parameters (different formulas for different distortion models)
- Iterate to refine parameters

## Camera Calibration Result
- Output of calibration process is an estimate of camera intrinsic parameters (camera matrix, lens distortion parameters)
- Allows for accurate mapping between image coordinates and world coordinates

## Alternative Methods
- Calibration using planar surfaces in the world
	- Advantage: no need for a special calibration target
	- Disadvantage: more difficult to detect/track keypoints, may introduce errors
- Look up camera parameters from manufacturer specifications
	- Advantage: no computation!
	- Disadvantage: only for cameras with fixed focal length

## Summary
- Camera calibration is used to recover a camera's intrinsic parameters, expressed as a camera matrix
- Calibration is required for application that involve accurate mapping between world and image points (e.g., augmented reality)

# Epipolar Geometry - Basics
## Two-View Problem
- What is the camera transform (translation + rotation) that relates these two views?
- As the dots show, we can map matching features together from two different perspectives. We can then ask what are the governing rules that govern how these features map between scenes.

![[Computer Vision/Images/two_view_problem.png]]

## Estimating Camera Transform
- When camera $C$ or $C'$ take a photo of point $X$, it is represented as point $x$ or $x'$ on the image plane
- We can say that there exists some relationship between $C$, $C'$, $x$, $x'$ and $X$, which is represented by the epipolar plane $\pi$.
- If we know about the transformation from camera $C$ and camera $C'$, and we know about point $X$ and its position in the image for both cameras, we can find the correspondence between the two images which lies along the intersection between the images and the epipolar plane.
- This idea is similar to that of the stereo example, but we can work in varying $y$ coordinates as well or whatever transformations.

![[estimating_camera_transform.png]]

## Key Idea: Epipolar Constraint
- Basically we can see that there is a form of mapping between the two images aimed at a single point in the world. 
- $l$ and $l'$ are lines that map the camera to a point in the world and the other camera, any point along thing plane can also be seen by the other camera, as demonstrated in the second figure below.
- We call this intersecting line that creates this mapping the epipolar line $l$ and $l'$. 

![[key_idea_epipolar_constraint.png]]

![[key_idea_epipolar_constraint2.png]]

Potential matches for $x$ must lie on the line $l'$
Potential matches for $x'$ must lie on the line $l$

## Epipolar Geometry: Notation

![[epipolar_geometry_notation.png]]

## Example: Epipolar Lines
- As we can see in the images below, we have keypoints on the vases.
- On the opposite image, we have the matching keypoint which is the keypoint's correspondence.
- From the geometry of the camera, we also have the line in the opposite image to search for the keypoint's correspondence.
- Somewhere way off outside of the image, the four lines in an image will all meet at one point, and this point is the epipole.
	- For the left image, it will be off to the right. Vice versa for the right image.

![[epipolar_lines.png]]

## Example: Horizontal Motion
- For horizontal motion, the lines are parallel and thus the epipole is infinitely far away.

![[example_horizontal_motion.png]]

## Example: Forward Motion
- These two below images show motion in the forward direction

![[example_forward_motion1.png]]

![[example_forward_motion2.png]]

![[example_forward_motion3.png]]

## Summary
- Epipolar geometry describes relations between points in two views
- A point in one image lies along an **epipolar line** in the other image
- Epipolar lines in an image meet at a point called the **epipole**
- The epipole is the projection of one camera in the other image

# Epipolar Geometry - Math
## Two-View Problem

![[estimating_camera_transform.png]]

## Epipolar Constraint

![[epipolar_constraint.png]]

- If the camera matrix ($K$) is known, use it to convert image points $x$ and $x'$ to homogeneous coordinates
- This formula is how we convert real world coordinates to image coordinates, where $X$ is the point in the real world, and matrix $K$ is the camera matrix
- $\hat{x}$ in this case is the $x$ in the above diagram
$$
\hat{x} = \underset{K} { 
\begin{bmatrix} 
   f_x & 0 & c_x\\  
   0 & f_y & c_y \\
   0 & 0 & 1  
\end{bmatrix}} X
$$
- The below figure should be fairly explanatory.

![[epipolar_constraint2.png]]

- To get the point as seen from either view, we simply apply the below formula.
- What we should remember is also the key constraint below.

![[epipolar_constraint3.png]]

- The first left bottom formula shows us that the cross product of $T \times R \hat{x}$ is zero when dot product is performed with $\hat{x}'$ since it is perpendicular
- The second formula basically condenses the cross product into a matrix notation and moves out the $\hat{x}$ to then turn the cross product matrix into the form $E$.
- We can therefore consider this $E$ as the essential matrix.

![[epipolar_constraint4.png]]

## Properties of Essential Matrix

![[epipolar_constraint.png]]

- $E\hat{x}$ is the epipolar line $l'$, $E^T \hat{x}'$ is the epipolar line $l$
- $Ee'=0$ gives the epipole $e'$ in the right image (projection of left camera's centre $C$)
- $E^Te=0$ gives the epipole $e$ in the left image (projection of right camera's centre $C'$)
- $E$ has 5 degrees of freedom (rotation (3) + translation (2) missing a scaling factor)

## Fundamental Matrix
- What if the camera parameters are unknown (uncalibrated cameras)
- Can define a similar relationship using the unknown $K$ and $K'$:
- As we can see with the below equations, we can make our derivations from the first three equations and then boil this down to a matrix that can translate a point in one image to the same point in another image, which we call the _fundamental matrix_.

![[fundamental_matrix.png]]

## Properties of Fundamental Matrix

![[properties_of_fundamental_matrix.png]]

- $Fx$ is the epipolar line $l'$, $F^Tx'$ is the epipolar line $l$
- $F$ has 7 degrees of freedom (3x3 matrix, but it has an unknown scaling factor and $Det(F)=0$, which removes 2 degrees of freedom)

## Solving for $F$
- How to solve for $F$ when $K$ and $K'$ are unknown?
- Match pairs of points across views and find matrix $F$ that explains the correspondences

![[solving_for_F.png]]

- 8-point algorithm
	- Requires 8 matching points
	- Solve for $F$ as a linear system of equations
	- Additional steps (SVD = singular value decomposition) to ensure that $F$ has the correct form

![[solving_for_F2.png]]

## The 8 Point Algorithm
- Considering that $p$ and $q$ are correspondences for points in two different images, each correspondence between two views gives us:

![[8pontmatrix.png]]

- This equation can be expanded out to give us:

![[eqtation8pont.png]]

- This is a single linear constraint on the values of $F$ and can be rewritten in matrix form

![[point8matrxi.png]]

- So with 8 correspondences we get a matrix like this (where each row has the $p$ and $q$ from different correspondences)

![[8poingmatrix.png]]

- The vector of $F$ values that we want is the null space of this matrix

## Limitations
- System is only solved up to a scaling factor, need to least one known distance to solve for real-world positions
- Degenerate cases: can't solve if the system has too few degrees of freedom
	- Points in the world are all coplanar
	- Camera translation = 0 (just rotation)

## Example: Refining GPS Location
- Algorithm:
	- Detect ASIFT (affine-invariant SIFT) keypoints in each image, find the potential matches using ratio test
	- Use RANSAC to find the Fundamental matrix ($F$) that relates the two views, and the inlier matches that are explained by that transform
	- Compute Essential matrix ($E$) from just $F$ using known camera matrices
	- Decompose $E$ into rotation and translation between cameras

![[refining_gps_location.png]]

## Summary
- Epipolar geometry describes how a point in 3D space is imaged through a pair of cameras
- Essential and Fundamental matrices map point in one image to a line (epipolar line) in the other image
- Typically, use feature detection to find matching points in the two views, then solve for Fundamental matrix (e.g., using RANSAC)