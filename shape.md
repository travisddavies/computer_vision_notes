## Shape 

![[shapes.png]]

## Models of Shapes
- Models of 2D shape are usually based on either:
	- The bounding contour of the shape (segments, angles)
	- The internal structure of the shape (branches)

![[models_of_shapes.png]]

# Shape Skeletons
## Shape Skeleton
- Topological skeleton = thinnest possible version of a shape
- Formed of lines that are equidistant from the boundaries of the shape

![[shape_skeletons.png]]

## Geometrical Description
- The skeleton points are the centrepoints of the largest discs that can be fit inside the shape
- If the shape was painted with a circular brush (of variable radius), the skeleton would be the path of the brush

![[rename_geometrical_description.png]]

## Skeletonisation Algorithm
- Grassfire transform - algorithm for shrinking or thinning a shape
- For each pixel within the shape, compute distance to closest boundary; peaks in the distance map are the skeleton

![[skeletonisation_algorithm.png]]

## Skeleton Representation
- Skeleton + distance to boundary at each skeleton pixel is a compact, invertible representation of shape
- To "inflate" skeleton, place a disc at each skeleton pixel (radius = distance to boundary at that pixel)

![[skeleton_representation.png]]

## Application: Shape Recognition
- Shape skeletons are easily converted to graphs
- Graph representation can be used for shape matching, pose recognition

![[application_shape_recognition.png]]

## Application: 2D $\rightarrow$ 3D
- Shape skeletons can also be used as the basis for a simple 3D model - just "inflate" with spheres instead of disks

![[application_2d_3d.png]]

![[application2d_3d_2.png]]

## Drawbacks to Skeletons
- Shape must be segmented from background

![[drawbacks_to_skeletons.png]]

- Small changes in shape boundary produce large changes in skeleton

![[drawbacks_to_skeletons2.png]]

## Summary
- Shape skeletons represent the internal structure of shapes
- Skeleton representations work well to model shapes that have skeleton-like structure
	- Human/animal figure
	- Written characters
	- Paths/networks (e.g., city roads, blood vessels)

# Contour Representations
# Automatic Contour Detection?

![[automatic_contour_detection.png]]

## Active Contours (Snakes)
- Parametric model that fits itself to object boundary
- "Shrink wraps" around object to capture shape

![](https://www.inf.u-szeged.hu/ssip/2007/teams/team6/gvfsnake1.gif)

## Active Contour Algorithm
- Initialise contour outside object boundary
- On each step, allow each point on the contour to shift 1 pixel in any direction:
	- Shift to minimise a loss (or energy) function:
$$
E_{total} = \alpha E_{elasticity} + \beta E_{stiffness} + E_{edge}
$$
- Repeat until loss does not change

$$
E_{total} = \alpha E_{elasticity} + \beta E_{stiffness} + E_{edge}
$$
- $E_{elasticity}$ is based on contour length, penalises longer contours
- $E_{stiffness}$ is based on contour curvature, lowest for straight contour segments
- $E_{edge}$ is based on image gradient at contour locations, lowest where image gradient is highest 
- $\alpha, \beta$ are free parameters

## Application: Segmentation
- Active contours are used for segmentation and tracking, particularly in medical image analysis

![[application_segmentation.png]]

## Drawbacks to Active Contours
- Requires initialisation (often from a human annotator)
- May not fit shape correctly
	- Trade-off between elasticity/smoothness and edge-matching - may fail to fit concavities in complex shapes
	- Difficult to detect shapes in clutter

![[drawbacks_to_active_contours.png]]

## Summary
- Active contours fit a shape boundary
- Tries to find an optimal shape which is both well-fit to the edges and fairly simple (smooth, compact)
- Works well to segment objects with uniform appearance, moving objects

# Face Models

## Face Models
- It's difficult to develop a general-purpose model of shape that can represent all possible shapes well
- However, it is possible to develop parametric models for particular classes of shape
- One very widely-studied class of shapes is the human face

## Eigenfaces
- If faces are aligned, pixel luminance values are sufficient to capture face shape
- Simple pixel-based model: eigenfaces

![[eigenfaces.png]]

## Eigenfaces Algorithm
- Each face is represented as a vector to the mean face image
- Parameters of face shape are obtained from PCA of face vectors

![[eigenfaces_algo.png]]

- Principle components of face vectors:

![[eigenfaces_alago2.png]]

## Eigenfaces
- Problem: Usually we can't assume faces appear in consistent alignment (or consistent lighting)!
- To model faces under real-world conditions, we need models that can consider shape/pose

## Active Appearance Models
- Labels corresponding landmark points in each image
- Warp images onto the mean shape to get shape-free texture

![[active_appearance_models.png]]

- Obtain "shape", "texture", and "appearance" (shape+texture) parameters through PCA

![[active_appearance_mdoels2.png]]

- To fit the model to a new face, use gradient descent to minimise difference between model and image
- Applications: face synthesis, face segmentation

![[active_appearance_models4.png]]

- Active appearance models separate shape and texture
	- Allows alignment of facial features, even when images are not aligned
- Problem: Shape is represented using 2D contours
	- Can't separate face shape vs. pose
	- Can't separate surface colour vs. lighting

## 3D Face Models
- 3D version of active appearance model: morphable 3D mesh + texture map
- Parameters based on PCA of a large 3D dataset

![[3d_face_models.png]]

## 3D Face Matching

![[3d_face_matching.png]]

## 3D Face Model Results

![[3d_face_model_results.png]]

## Application: Facial Recognition
- Most recognition algorithms use a shape model to align faces as a first step

![[application_facial_recognition.png]]

- Once faces are aligned, a standard CNN pipeline can be trained for face recognition
- Why is alignment critical for CNNs?

![[application_face_recognition2.png]]