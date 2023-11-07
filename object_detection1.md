# Object Detection

<iframe width="560" height="315" src="https://www.youtube.com/embed/WZmSMkK9VuA?si=9MXvwObKnFJguN9m" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Learning Outcomes
- Explain the standard approach to object detection (sliding window)
- Explain how object detection is implemented in region-based CNNs
- Explain object detection methods
- Explain design issues and trade-offs involved in building detection methods

# Object Detection Basics

## Classification vs. Detection

![[classification-v-detection.png]]

- Object detection = locate objects in an image
	- Classification: "Is this a $<\text{class label}>$?"
	- Detection: "Where is the $<\text{class label}>$?"
- Object detection is usually modelled as a classification task performed within patches of an image
	- Detection: For every patch, "Is this a $<\text{class label}>$?"

## Sliding Window Approach
- What the below images are demonstrating is the following:
	- Imagine we are sliding a window across our image to find our target.
	- Our target may be very wide, or very square, or very slender, or anything else
	- Our target may be very large, or very small, or anything in between
	- Our target could be anywhere in the image, so we would need to continually shift it across the image to search for it
	- These two combinations creates waaay too many combinations for our sliding window approach to be even slightly efficient

![[sliding-window-approach.png]]

![[sliding-window-approach2.png]]

![[sliding-window-approach3.png]]

- Free parameters:
	- Stride
	- Scale (size of window)
	- Shape (aspect window)
- Generally object dimensions are unknown, so a range of scales/shapes will be required

## Sliding Window Evaluation
- Another parameter: threshold for detection
- Windows over the threshold will be considered "target"
- Note that this makes evaluation tricky
	- Is the below image a good result?
		 - We can consider each of these not exactly capturing the essence of our lamp. So we will need a definitive metric to define this

![[sliding-window-evaluation.png]]

## Window Evaluation: IoU
- Common method to evaluate a window result is Intersection over Union (IoU) between true bounding box and detection window

![[iou-formula.png]]

## Example: IoU
- Our predicted box is $3 \times 7= 21$
- The overlap that we have with the target box is $18$
- The total area of union is the area of total area of the box, $6 \times 8 + 3 = 51$
- $IoU = 18/51 = 0.35$

![[example-iou.png]]

## Summary
- Object detection is generally modelled as image classification within small regions of an image
- Windows over some threshold = "detections", can be evaluated using IoU with ground truth
- Problems:
	- Very large number of possible windows (slow, increases probability of false detections)
	- Overall evaluation of images with multiple targets can be complicated (multiple targets, multiple detection windows, different IoUs)

# R-CNN
## Sliding Window Classification
- Very large number of windows per image (C scales x S shapes x N locations)
- How to classify efficiently in parallel?

![[r-cnn-sliding-window.png]]

## Sliding Window Classification
- Even in a neural network, classifying all possible boxes may be slow (may also increase false alarms)
- Solution? Focus on boxes most likely to be objects

![[sliding-window-classification0.png]]

## R-CNN
- R-CNN = Region-based convolutional neural network
- Given an image, identify a small number of windows for object detection ("region proposals" or "region of interest (ROIs)")

## Generating Region Proposals
- R-CNN uses Selective Search to generate ROIs
- Selective Search algorithm:
	- Oversegment image into superpixels
	- Iteratively combine adjacent superpixels based on similarity in colour + texture, size, and compactness

![[generating_region_proposals.png]]

## R-CNN: Region-Based CNN
- We first take an image

![[rcnn-region-based-cnn.png]]

- We take a bunch of regions of interest that or selective search selected (approximately 2000 in total)

![[region-based-cnn.png]]

- Since our regions of interest may not necessarily be square but various dimensions, we will then warp these patches to become square

![[r-cnn-region-based-cnn.png]]

- We then run each of these patches through a **single** CNN

![[region-based-cnn-2.png]]

- The CNN then predicts both the class of the patch AND the bbox of that class
- We will also perform regression with the predicted bbox on the ground truth bbox to teach the CNN to learn how to form the box around the object
- The factors of the bbox that it needs to learn are the transformed image's centre $x,y$ coordinates, as well as the box's height and width

![[region-based-cnn-3.png]]

![[cnn-region-based-cnn.png]]

## Bounding Box Computation
- Original region proposal = ($p_x, p_y, p_h, p_w$)
- Transform = ($t_x, t_y, t_h, t_w$)
	- Remember: this is just a factor of the original region proposal box so this needs to be rescaled to the actual dimensions of the image
- Goal: compute bounding box = ($b_x, b_y, b_h, b_w$)
- Step 1. Translate

$$
b_x = p_x + p_w t_x, \ \  \ \ b_y = p_y + p_h t_y
$$

- Step 2. Scale

$$
b_w = p_w \exp(t_w), \ \ \ \ \ b_h = p_h \exp(t_h)
$$

## R-CNN Training
- CNN pretrained on ImageNet
- Last layer (1x1000) is replaced with a new classification layer of size 1x(N+1)
	- $N+1$ = $N$  object classes + "background" class, i.e. the $(N+1)\text{th}$ class is _not interesting_ to us
	- CNN is retrained on (N+1)-way detection, using regions with IoU >= 0.5 as ground truth "objects"
	- Sample regions so 75% of training set is "background"
- CNN features are used as input to:
	- Label classification model (1-vs-all linear SVM)
	- Bounding box model (class-specific linear regression)
 
## R-CNN Testing
- Input test image
- Compute region proposals (Selective Search)
- Each region: run through CNN to predict class labels and bounding box transforms 
- "Detections" = regions with highest confidence scores
	- Based on a threshold, or top $k$?
	- Overall, or pre-category?
	- These are design choices that we can make
		- Often we just use threshold

![[r-cnn-testing.png]]

## R-CNN Results (Random Sample)

![[r-cnn-results.png]]

![[rcnn-results-authors-pick.png]]

## R-CNN Summary
- R-CNN (Region-based convolutional neural network) does classification in parallel over a set of region proposals
- Output: class labels and bounding box transforms
- Advantages
	- Much more efficient than classifying every window
- Disadvantages
	- Still requires classifying many windows (e.g., 2000)
	- Region proposal step could miss some objects

# Fast R-CNN

## Fast R-CNN
- Major change: Run the whole image through a _reasonably shallow_ fully-convolutional neural network
- Take region proposals from last convolutional layer
- What this means is that rather than taking samples from the image like we did for R-CNN, we are instead going to take samples from the feature map output of the CNN
- This saves us on computations, as there are many, many overlapping proposals and therefore redundant calculations

![[Computer Vision/Images/fast-rcnn.png]]

## Fast R-CNN
- As explained, we first take our imge

![[fast-rcnn 1.png]]

- We feed the image through our convolutional neural net and then take the feature map output of the last layer.

![[fast-rcnn2.png]]

- With these feature map, we will take our regions of interest.
- This will look at a _representation_ of the image rather than the pixel values of the image

![[Computer Vision/Images/fast-rcnn3.png]]

- Like in R-CNN, we crop and resize these regions of interest into squares

![[Computer Vision/Images/fast-rcnn4.png]]

## How to Crop/Resize Features
- So we map the bounding box to the last layer feature map columns (since the feature map is downsized)

![[how-to-crop-resize-features.png]]

- We then "snap" the bounding box to the closest columns on the feature map, i.e. making sure that the coordinates of the box are in line with the dimensions of the downsized feature map
- We then downsize our feature map into a 2x2x$n_{channels}$ representation with max pool within each grid cell and use this output for classification and bounding box predictions

![[how-to-crop-resize-features2.png]]

## Fast R-CNN
- We then feed these resized regions of interest into a CNN, which is much smaller and faster

![[fast-rcnn5.png]]

- We then receive our bbox and class outputs from the model

![[Computer Vision/Images/fast-rcnn7.png]]

- Backbone will be used to extract features from the image, i.e. AlexNet or ResNet
- The per-region network doesn't need to be complex since the output is so downsized and processed

![[fast-rcnn6.png]]

- What architecture to use for the "backbone" FCN and per-region networks?
	- AlexNet, ResNet, etc.

![[fast-rcnn7 1.png]]

![[fast-rcnn8.png]]

## Fast R-CNN Training
- Train on $R$ regions sampled from $N$ images
	- For efficiency, $N$ is small and $R$ is large (e.g., 64)
	- Sample regions so 75% of training set is "background"
- Train with a multi-task loss: $L=L_{cls} + L_{loc}$
	- $L_{cls} =$ cross-entropy loss over labels
	- $L_{loc} =$ SmoothL1 of $\text{abs}(\text{true}-\text{predicted bbox parameter})$
		 - This is so we have an acceptable first derivative of the L1 loss, as there will be discontinuities when L1 = 0
 - $L_{loc}$ computed for object classes only
	- i.e. if it is background, then we won't compute the loss, we will only compute the loss over the actual object classes
![[fast-rcnn-training.png]]

## Fast R-CNN Summary
- End-to-end region-based convolutional neural network
- Advantages:
	- Faster than R-CNN (~9x faster training, ~140x faster test)
	- Slightly more accurate than R-CNN
- Disadvantage:
	- ROIs aren't learned; region proposal step could miss some objects

# Faster R-CNN

## Faster R-CNN
- Major change: network learns region proposals, instead of Selective Search 
- The left-hand side are the new parts for faster CNN - a region proposal network

![[faster-rcnn.png]]

## Region Proposal Network (RPN)
- Each pixel in our downsampled representation of the image gets the chance to propose regions of interest, i.e. anchor points.
- We let the anchor point make $k$ proposals of what region that it belongs to, as well as a fixed size and aspect ratio for the region
- In each region, predict object class and bounding box transform

![[region-proposal-network.png]]

## Faster R-CNN
- For each region:
	- Crop & resize features
	- Predict object class and bbox transform
- For each image:
	- Run backbone CNN to get feature map
	- Compute region proposals from RPN

![[faster-rcnn.png]]

## Faster R-CNN Training
- RPN loss is weighted sum of:
	- Classification loss: binary cross entropy loss (any object vs. background)
	- Regression loss: Smooth L1 between true and predicted bbox parameters
- As in Fast R-CNN, training samples $R$ regions (anchors) from; $N$ images ($R=256, N=1$)
	- Anchors are sampled so up to 50% are objects
- Full network is RPN + Fast R-CNN (sharing a backbone)
	- Various ways to train this, but original method alternatives between training RPN and Fast R-CNN 
 - Generally first train Region Proposal Network, and then train the final layer that classifies bbox and class

## Faster R-CNN Summary
- Faster R-CNN is similar to Fast R-CNN but learns the region proposals with a region network (RPN)
- Even faster than fast R-CNN (~10x faster test)
- Modular approach - variations on Faster R-CNN with deep backbone tend to be quite accurate
	- Speed-accuracy trade-off: deeper networks are also slower

## Summary
- Object detection = classification of image regions
- Exhaustive search is slow; most methods use only a subset of regions ("region proposals" or "regions of interest (ROIs)")
- Many parameters to consider:
	- What counts as a true detection / true rejection (IoU threshold)?
	- How to select region proposals?
	- How to deal with class imbalance? ("background" is most common class)