# Image Segmentation I

## Segmentation

![[segmentation.png]]

## Semantic Segmentation

![[semantic_segmentation.png]]

## Instance Segmentation

![[instance_segmentation.png]]

![[instance_segmentation2.png]]

## Learning Objectives
- Implement clustering algorithms for segmentation and compare/contrast clustering methods
- Implement an algorithm for computing superpixels and explain their common applications
- Explain graph-based methods for image segmentation

# Pixel Clustering

## Colour Clustering

![[colour_clustering.png]]

## K-means ($k = 6$)

![[k-means_6.png]]

## Gaussian Mixture Model ($k=6$)

![[gaussian_mixture_model_k6.png]]

## Mean Shift (bandwidth = 7)

![[mean_shift_bandwidth7.png]]

## Mean Shift Clustering

![[Computer Vision/Images/mean_shift_clustering.png]]

## Mean Shift Clustering
- Assume points are samples from an underlying probability density function (PDF)
- Compute peaks of PDF from density of points

![[mean_shift_clustering1.png]]

## Mean Shift

![[mean_shift1.png]]

![[mean_shift2.png]]

![[mean_shift4.png]]

![[mean_shifst5.png]]

![[mean_shift6.png]]

![[mean_shift7.png]]

![[mean_shift8.png]]

## Mean Shift Algorithm
- Compute mean shift vector $m(x)$
- Translate kernel window by $m(x)$

![[mean_shift_algorithm.png]]

- For each point:
	- Centre a window on that point
	- Compute the mean of the data in the search window
	- Centre the search window at the new mean location
	- Repeat (b,c) until convergence
- Assign points that lead to nearby modes to the same cluster
- Free parameters: kernel (commonly Gaussian), bandwidth

## Mean Shift Segmentation
- Cluster in spatial+colour space; e.g.: ($x,y,R,G,B$) or ($x,;y,L,A,B$) coordinates

![[mean_shift_segmentation.png]]

## Mean Shift Parameters

![[mean_shift_parameters.png]]

# Superpixels

## Superpixels
- **Oversegmentation** methods segment image into regions that are smaller than objects
	- Objects are separated from background
	- But objects are also separated into many parts
- Superpixels = groups of adjacent pixels with similar characteristics (e.g., colour)

## Superpixel Segmentation

![[superpixel_segmentation.png]]

## SLIC Superpixel Algorithm
- Initialise cluster centres on non-edge pixels:
	- Initialise $k$ cluster centres $c_k = [x_k, y_k, I_k, b_k]$ by sampling the image in a regular grid
	- For each centre $c_k$, check an N x N neighbourhood around $c_k$ to find the pixel with lowest gradient. Set $c_k$ to this pixel's $[x,y,l,a,b]$

- For each cluster centre $c_k$:
	- In a 2M x 2M square neighbourhood around $c_k$, measure pixel similarity to $c_k$
	- Assign pixels with similarity < threshold to cluster $k$
	- Compute new cluster centre $c_k$
- Repeat until average change in cluster centres (L1 distance) falls below a threshold
- Similarity measure:

![[similarity_measure.png]]

- Similarity metric does not guarantee that clusters will be connected pixels
- To enforce connectivity, pixels not connected to main cluster are re-assigned to closest adjacent cluster

## Superpixel Methods

![[superpixel_methods.png]]

## Superpixel Applications
- Superpixel applications
- More compact representation for algorithms with high time complexity (600x800 pixels -> 200 superpixels)
- Common application: object segmentation
	- Oversegment image
	- Combine superpixels to find objects

## Superpixel Merging
- Region Adjacency Graph (RAG)
	- Vertices = image regions (pixels or superpixels)
	- Edge weights = difference between regions
- To merge superpixels:
	- Identify edges below a threshold and re-label superpixels connected by these edges as one region
	- Or iteratively:
		- Find lowest-weight edge, relabel connected superpixels as one region
		- Recompute RAG, repeat until a criterion is met (e.g., all edges above a threshold)

![[superpixel_merging.png]]

# Graph-Based Segmentation

## Images as Graphs
- Represent image as a graph $G = (V,E)$
	- Vertices = image regions (pixels or superpixels)
	- Edge weights = similarity between regions

![[images_as_graphs.png]]

## Graph Cuts
- Consider image as a fully-connected graph
- Partition graph into disjoint sets $A,B$ to maximise total edge weight = remove low-weight  edges between dissimilar regions
- Minimise value of cut:

$$
cut(A,B) = \sum_{u \in A , v \in B} w(u,v)
$$

$w(u,v)$ represents the weight of edge connecting $u$ and $v$

## Graph Cuts
- Not ideal for image segmentation - tends to create small, isolated sets

![[graph_ccuts.png]]

## Normalised Cuts
- Instead of minimising cut value, minimise cut value as a fraction of total edge connections in entire graph (normalised cut)
- Normalised cut (Shi & Malik, 2000)

$$
Ncut(A,B) = \frac{cut(A,B)}{assoc(A,V)} + \frac{cut(A,B)}{assoc(B,V)}
$$

$$
Ncut(A,B) = \frac{\sum_{u \in A, v \in B}w(u,v)}{\sum_{u \in A, t \in V} w(u,t)} + \frac{\sum_{u \in A, v \in B}w(u, v)}{\sum_{v \in B, t \in V}w(v,t)}
$$

## Normalised Cuts Results

![[normalised_cuts_results.png]]

## GrabCut
- Segments image pixels into just two classes: foreground (object) and background
- Uses colour clustering + graph cuts to find optimal classification of pixels into each class

![[grabcut1.png]]

## GrabCut Algorithm
- Requires user to initialise algorithm with a bounding box

![[grabcut_algorithm.png]]

- For each class (foreground, background), represent distribution of pixel colour as a Gaussian mixture model (GMM)
- Represent image pixels as a graph (8-way connectivity)

![[grabcut_algorithm2.png]]

- Denote the pixel graph as $G$ and the GMM as $\theta$
- $\alpha$ indicates label of each pixel (foreground or background)
- Iterate until convergence:
	- Find graph cut (label assignment) to minimise
 
		![[graphcut_formula.png]]
	- Recompute GMM for new label assignment

## GraphCut Example

![[graphcut_example.png]]