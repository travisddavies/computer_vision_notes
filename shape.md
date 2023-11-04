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