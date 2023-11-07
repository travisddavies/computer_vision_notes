## Object Detection II

![[find_this.png]]

![[find_this2.png]]

![[find_this3.png]]

![[find_this4.png]]

![[find_this5.png]]

![[find_this6.png]]

![[find_this7.png]]

![[find_this8.png]]

## Learning Outcomes
- Explain how single-stage object detectors differ from two-stage methods like Faster R-CNN
- Implement algorithms to do non-maximum suppression (NMS) and compute mAP
- Compare and contrast various approaches to object detection

# Single-Stage Object Detectors

## YOLO (You Only Look Once) v1
- Main idea: instead of going through multiple steps (region proposals, region classification), just predict a heatmap for each class directly in a CNN

![[yolo.png]]

![[YOLO2.png]]

- Output is a set of $N$ class probability maps + $M$ bounding box parameter maps
- Loss is sum-squared error between true and predicted maps, with some weighting:
	- Bbox location parameters get higher weight in the loss
	- Grid cells that don't contain objects don't contribute to classification loss
	- Bbox parameters are penalised based on their confidence, encouraging the $M$ bboxes to specialise for different objects

- Advantages:
	- Fast
	- Accurate, for a real-time object detector
- Disadvantages:
	- Limited spatial precision
	- Generally less accurate than slower detectors
- (There have been multiple versions of this algorithm that have improved the original method)

## SSD: Single Shot Multibox Detector
- Similar to YOLO: instead of generating region proposals, directly predict a set of class+bbox heatmaps
	- For each anchor point: $k$ bboxes * ($N$ class confidences * 4 bbox parameters)

![[ssd.png]]

- Major change: anchor points in multiple convolutional layers, allowing for detection at different scales

![[ssd2.png]]

![[ssd3.png]]

- Faster than regional-proposal methods like Faster-CNN
- Generally less accurate than regional-proposal methods
- Anchor points in early layers helps with spatial prediction and detection of small objects

## Alternatives to Bounding Boxes

![[alternatives-to-bounding-boxes.png]]

- Other options? Oriented bounding box, oriented ellipse, Gaussian distribution, oriented Gaussian?

# Instance Segmentation

## Instance Segmentation
- Semantic segmentation classifies pixels, doesn't distinguish between instances
- How to separate instances?

![[instance_segmentationn.png]]

- Common method:
	- Run object detector, extract bounding boxes and labels
	- Do binary (foreground/background) segmentation within each bounding box
- Commonly-used architecture: Mask R-CNN

## Faster R-CNN

![[faster-rcnn.png]]

## Mask R-CNN
- Basically just an extra step on Faster R-CNN - each patch runs through a fully-convolutional network that predicts a binary segmentation mask
- Patch loss becomes: $L_{cls} + L_{box} + L_{mask}$

![[mask-rcnn2.png]]

## Mask R-CNN Results

![[mask-rcnn-results.png]]

<iframe width="560" height="315" src="https://www.youtube.com/embed/EfaCtOEEmLA?si=Me-SUbJ--tiruDsi" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
## Summary
- Instance segmentation can be modelled as object detection followed by binary segmentation (foreground/background)
- Common architecture is Mask R-CNN, which is a modification of Faster R-CNN

# Evaluating Object Detectors

## Object Detection Result
- Typically, object detectors will return overlapping detections
	- Can be different objects, or the same object detected at multiple scales/positions
- Treat as multiple detections? Or select one as the final prediction?

![[object_detection_result.png]]

## Non-Max Suppression (NMS)
- Typical approach: non-maximum suppression (NMS)
- Algorithm:
	- Starting with the highest-scoring bounding-box...
	- Drop bounding boxes with lower score that overlap with this box above some IoU threshold (e.g., 0.7)
	- Repeat with next highest-scoring bounding box
- Often done separately within each object class

![[non_max_suppression.png]]

![[non-max-suppression2.png]]

![[non-max-suppression3.png]]

- NMS can drop some correct detections when objects are highly overlapping
- But generally this is preferable to counting the same object many times

![[nms-4.png]]

## Evaluation
- How to evaluate, given that there may be multiple objects/detections per image?
- Commonly-used method:
	- Run detection on entire test set
	- Run NMS to remove overlapping detections
	- For each object category, compute Average Precision (AP) = area under precision-recall (P-R) curve

![[evaluation-nms.png]]

![[evaluation-nsm2.png]]

![[evaluation-nms3.png]]

![[evaluation-nms5.png]]

![[evaluation-nms6.png]]

![[evaluation-nms7.png]]

![[evalutation-nms8.png]]

![[evaluation-nsm9.png]]

## Properties of P-R Curve
- What is the best possible AP (area under P-R curve)?
- How would you accomplish this?

![[properties-of-pr-curve.png]]

## Evaluation

![[evaluationnnn.png]]

## Mean Average Precision (mAP)
- Example:
	- Bird AP = 0.65
	- Cat AP = 0.80
	- Dog AP = 0.86
	- mAP@0.5 = 0.77
- "COCO mAP": Compute mAP for multiple IoU thresholds (0.5, 0.55, 0.6, ..., 0.95)
	- Example: mAP@0.5 = 0.77, mAP@0.55 = 0.72, ... mAP@0.95 = 0.19
	- COCO mAP = 0.45

## Evaluation Summary
- Common metric for evaluating object detectors is mAP (or COCO mAP)
- Both NMS and P-R steps require IoU thresholds; different thresholds can change results
- Object detection is complex - one number is not very informative
	- How accurate is the object classification?
	- How accurate are the bounding boxes?
	- What kind of errors is the model making (misses, false alarms)?

# Beyond Patches?

## Scene Priors

![[scene_priors.png]]

![[scene_priors2.png]]

![[same_priors3.png]]

## Scene Context

![[scene_context.png]]

## Object Detection in Context
- Scene context provides both global and local priors:
	- Global prior: likelihood of the object appearing at all
	- Local: likelihood of the object in given location
- Including these priors can help reduce false detections
- Is there a downside to including these priors?