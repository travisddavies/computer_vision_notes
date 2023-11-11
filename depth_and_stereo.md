# Depth and Stereo
## Learning Outcomes
- Explain the cues available for depth perception in single-view and multi-view images
- Compute depth from disparity
- Explain how a standard stereo depth algorithm works
- Explain machine-learning approaches to single-view depth

# Cues for Depth
## 1. Perspective
- We can see that the smaller rectangle in the centre has connecting diagonal shapes on the sides coming towards us.
- This gives us the perspective that this is something like a room and goes inwards to the smaller rectangle.

![[perspective.png]]

- Much can be seen for these images, we can use our senses to tell that these buildings and objects a further way since the objects and buildings look relatively small compared to the larger objects which we assume are closer.

![[perspective2.png]]

## 2. Texture
- The floor in the left image is actually flat, however the _texture_ makes us believe that the floor is actually deeper on the middle part of the floor. (Looking at the floor, we expected all the patterns to be the same but it is not and gives us an alternative perception)
- Texture plays an important role on our perceptions for how deep something is.

![[texture.png]]

## 3. Object Position and Occlusion
- The position of objects also has a strong influence on our perception of depth. 
- As we can see with the cylinders, placing some higher than others with shadows makes us believe that the higher cylinder has been placed _behind_ the other cylinders
- The shapes on the left hand side also make us believe that the blue shapes are _in front_ since they are placed over the orange shapes.

![[object_position.png]]

## 4. Object Knowledge
- With objects that we know about, we can interpret the depth of the image.
- We know how big the Leaning Tower of Pisa is, so we know that the building is actually far away from the man pretending to hold it
- Much the same for the other image, we know it's not possible to have this size difference by looking at the bodies of the two women, so we can tell that the smaller women is further away from the camera (This technique was used in LOTR for the Hobbits in Shire)

![[object_knowledge.png]]

## Binocular Stereo
- This is done by separating an object at different distances and colours so that when you wear 3D glasses, different colours for the separated objects are filtered for one eye and not for the other(one glass allows red and the other blue) to trick your brain into thinking that there is some distance with the object.

![[binocular_stereo.png]]

## Motion Parallax
- Motion parallax is basically showing different perspectives on the same objects in an image, like this video taken by a drone of different parts of Melbourne University

<iframe src="https://player.vimeo.com/video/231242851?h=26303ef259" width="640" height="360" frameborder="0" allow="autoplay; fullscreen; picture-in-picture" allowfullscreen></iframe>

## Cues for Depth
- 2D images contain a variety of information for depth perception
- Cues available in a single view include perspective, texture, and object cues
- More accurate depth information can be obtained by **combining multiple views** (**stereo**, **motion**)

# Stereo
## Depth from Stereo
- Stereo pair: images from two cameras with a horizontal shift in camera position
- Take note that the perspective of images of the object is slightly different, this gives us information to extract the depth of the image

![[Computer Vision/Images/depth_from_stereo.png]]

## Depth from Stereo
- Assume:
	- Image planes of cameras are parallel to each other and to the baseline $B$
		- Baseline $B$ is the distance between the two cameras
	- Camera centres are at the same height
	- Focal lengths $f$ are the same
	 - $x$ and $x'$ are distances of the camera's focal point on the plane below to the axis towards the point of interest. 
		 - $x$ is positive, $x'$ is negative.
- Goal: find $z$ (the distance on the right part of the image which could give us a perception of **depth**)

![[depth_from_stereo 1.png]]

## Depth from Stereo
- Solve for $z$:
$$
\frac{z}{B} = \frac{f}{x-x'}
$$
$$
z = \frac{fB}{x-x'}
$$
- Distance $z$ is inversely proportional to disparity $x-x'$ (For larger $z$, the error rate increases in terms of pixels)
- If you think about what this means it makes perfect sense 
	- If the angle of the camera's view to the axis intercept to the point of interest is large it means that the point is closer to the camera. 
	- If the angle is very small, then it means that the point is very far from the camera, much like when you see an object far in the distance.
- The extra angle on the side is the equivalent of the angle on the right and side of the image, this just makes it simpler for calculation.

![[depth_from_stereo_calc.png]]

## Basic Stereo Matching Algorithm

![[abe_lincoln.png]]

- For each pixel $x$ in one image
	- Scan a horizontal line in the other image, find best match $x'$
	- Compute disparity $x-x'$ and compute $depth = \frac{fB}{x-x'}$ 

## Basic Stereo Matching
- What we will do to find $x'$ in the second image is to scan along the pixels at the same $y$ coordinate with a window to find the the pixels where the sum of squared errors for the pixel intensity is the minimum value
- The point with the lowest sum of squared error will be considered $x'$ 
- Sum of squared errors for this task can often be unreliable, because changes in intensity from changes in light in the second image or different camera used can greatly influence the performance for finding $x'$

![[basic_stereo_matching.png]]

- What we can do instead is take the normalised correlation of the two patches of pixels, the pixel with the highest value for the normalised cross correlation will be considered to be the chosen $x'$

![[basic_stereo_matching2.png]]

## Effect of Window Size
- As shown in the images below, the size of the window can greatly influence the performance of the depth maps calculated.
- Window too small will add lots of noise into the depth map
- Window too large will make the image too blobby, because it's covering too much of the image and missing lots of important information.

![[effect_window_size.png]]

- Smaller window = finer detail, but more noise
- Larger window = smoother depth, but lacking detail
## Match Failures
- There are lots of ways that this approach can fail, however. 
- Take for example the gate in the below image, there's lots of repeated patterns, so it the algorithm will struggle to find the true $x'$ in the image.

![[paris.png]]

- Another failure is reflective objects. 

![[reflective_objects.png]]

## Window Matching Results
- As we can see in the below images, our final estimated depth can be messy(noisy) based on various factors.

![[window_matching_resuls.png]]

## Additional Constraints?
- Individual matches are often ambiguous
	- It is often difficult to find the exact pixels that correspond to a pixel in a second image
- However, the set of matches should obey additional constraints:
	- **Uniqueness**: a point in one view has no more than one match in the other view
	- **Ordering**: corresponding points should be in the same order in both views
	- **Smoothness**: disparity values should change smoothly (for the most part)

## Ordering Constraint
- The above-mentioned ordering constraint can sometimes not hold. ($d,b,a$  vs $c', b', d'$ which is the opposite order)
- As can be seen in the second image, objects can interfere with the two cameras and affect our calculations of depth. Therefore ordering will not always guarantee accurate results.

![[ordering_constraint.png]]

## Applying Constraints
- We can add into our constraints the sum of the squared errors of the pixel intensities (left part of equation)
- Then also add a penalty for changes in depth, which basically takes into consideration that a big change in depth at a certain point may be a point where a foreground part becomes a background part.
- This requires more advanced techniques such as graph cuts

![[applying_constraints.png]]

Minimise $E(D)$ using an optimisation such as graph cuts

## Window Matching Results
- Now when we look at the results from the smoothing of graph cuts as shown above, we get a much smoother result.

![[window_matching_results_graph_cuts.png]]

## Rectification
- What if the image planes are not parallel?
- **Rectification** finds projective transform that maps each image to the same plane

![[rectification.png]]

## Summary 
- Stereo depth is computed from disparity (difference in position of points in two views of a scene)
- Difficult to solve in practice because individual point matches are ambiguous 
- Solution generally involves additional constraints (e.g., smoothness)

# Single-View Depth
## Depth from Single Images
- We can actually get a lot of information from a single image, just like how we can take a lot of information of the depth from the below image.
- With deep learning, we can actually teach computers to learn this information too, by creating datasets that include heat maps for depths in images.

![[depth_from_single_images.png]]

## Supervised Depth Classification
- Treat depth estimation as a classification task: For each pixel in image, predict distance from camera
- Train on images with annotated depth maps (blue is closer, red is further)

![[supervised_depth_classification.png]]

- The below image is just an early architecture for learning the depth of images

![[supervised_depth_classification_cnn.png]]

## Loss Function?
- Images may have a very large range of depths - a loss based on **log(depth)** may work better than absolute depth
- Mean depth of scenes can vary widely (e.g., closet vs. stadium). To discourage models from simply learning mean depth, consider scaling the loss function so that it is similar for different scenes

## Supervised Classification Results

![[supervised_classification_results.png]]

## Depth from Disparity
- **Instead** of training on annotated depth maps, train on **stereo image pairs**
- Advantage: stereo image pairs can be produced with standard cameras, while depth maps require special equipment (e.g., LiDAR)

- Input: one image from a stereo pair (e.g., left)
- Learn to predict the disparity map that will produce the other image (e.g., right)
- Distance from camera to surfaces can be computed from the disparity map
- Train on stereo pairs

- Generally the way that you would train the model is shown below.
	- Train the CNN on the left and right images, 
	- Let it predict the disparity maps of the opposite image
	- Synthesise the input image with the disparity map to get the predicted image of the opposite side.

- Loss is sum of:
	- Appearance loss (difference between original and predicted image)
	- Disparity smoothness loss
	- Left-right consistency loss (difference disparity maps)

![[depth_from_disparity_cnn.png]]

## Depth from Disparity Results (yellow is closer, purple is further)

![[disparity_results.png]]

## Summary
- Machine learning methods can be trained to estimate depth from single images
- Advantages:
	- Does not require multiple views / stereo camera
- Disadvantages:
	- "Blurry" depth at object edges (but can be combined with edge maps for better results)
	- Models may not generalise well to new contexts

- 2D images provide multiple cues for 3D depth (single-image and multi-image cues)
- It is possible to compute depth from single images, but **more accurate depth measurements** can be obtained from **multiple views (e.g., stereo)**

# Exercises

![[depth_and_stereo_exercise1.png]]

- Computed depth is $z = \frac{fb}{\alpha + \beta}$
- Camera calibration is important for stereo vision because all inferences depend directly on the geometric parameters of the system. For example, the focal length must be the same value for both cameras for us to calculate the depth of the object
- Baseline, the $x$ and $y$ coordinate of each camera, the focal length, the rotation around each camera's optical axis