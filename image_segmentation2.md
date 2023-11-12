# Image Segmentation II

## Learning Outcomes
- Explain how semantic segmentation is performed by fully-convolutional networks
- Implement max unpooling and transposed convolution
- Explain two common architectures for segmentation problems (U-Net and Mask R-CNN)

## Segmentation

![[segmentation.png]]

## Semantic Segmentation

![[semantic_segmentation.png]]

## Instance Segmentation

![[Computer Vision/Images/instance_segmentation.png]]

![[instance_segmentation2.png]]

# Segmentation as Classification

## Pixel Classification
- Image segmentation as a classification problem
- Given a window (NxN) pixel, classify central pixel

![[pixel_classification.png]]

- Classifying individual pixels is potentially very slow (e.g., a small image = 600x800 = 480,000 pixels)
- But a CNN can classify multiple pixels in parallel

![[pixel_classification2.png]]

## Parallel Patch Classification
- Basically what the image below is showing is that we have a sliding window the size of the top image that slides over the bottom image. 
- This sliding window will find the region of the size of the top classifier model that most likely belong to the class "tabby cat", as shown with the heat map on the right hand side picture
- This model will be sampled at each of the yellow dots on the left hand image to generate this heat map
- This operation of patching is done in parallel, since the points are independent
- This is what we can denote a "fully-convolutional network"

![[parallel_patch_classification.png]]

## Fully-Convolutional Network
- Fully-connected network (FCN) has only convolutional layers, no fully-connected layers
- Last layer is a spatial map
- Can accept any size image as input, output map size depends on input size

## Parallel Patch Classification
- Standard CNN architecture poses two problems:
	- Receptive field size is linear with the number of convolutional layers
		 - Think about this for a second, as we increase the number of conv layers, the **receptive field** will only increase linearly. So we are not getting much bang for our buck with computation
	- Most methods downsample (e.g., with maxpooling) to reduce computation

![[paralllel_patch_classification1.png]]

- Solution: use encoder-decoder structure
- Encoder **downsamples** image, decoder upsamples
- We do this so that we can have a high-resolution output, but is also computationally efficient by reducing the size of the convolutional layers

![[parallel-patch-classification-encoder-decoder.png]]

## Upsampling
- How to upsample a feature layer in a CNN?

![[upsampling.png]]

## Max Unpooling
- Below shows the method of max unpooling
- What we essentially do is remember the index of where the max element came from during each stride so when we upsample, we can place each of the downsampled values into the original max index.
- The rest of the areas that are not filled in are left as zeros.
- This is so we don't keep putting the upsampled values in the same locations, which will impact the learning of our models

![[max_unpooling.png]]

![[max_unpooling2.png]]

## Convolutional Review
- Just to remember, below it shows that the stride of our convolutional operation affects the dimensions of our output
- Larger the stride, smaller the output

![[convolutional_review.png]]

![[convolutional_review2.png]]

## Transposed Convolution
<iframe width="560" height="315" src="https://www.youtube.com/embed/96_oGE8WyPg?si=wXwE4VN8hL7ICFfH" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
- Convolution with stride > 1 does a form of downsampling
	- E.g., stride = 2 means filter 2 pixels in input for every 1 pixel in output
	- "Learnable downsampling"
- Can we reverse this to do upsampling?
	- E.g., filter moves 2 pixels in _output_ for every 1 pixel in _input_
 - **Transposed convolution**: convolution with a stride < 1
	- In some papers, may also be called deconvolution, upconvolution, fractionally strided convolution

- The way that the process works is like so, but as a 1D case first:
	- For every value in the input, we multiply it with every value in the filter
	- Each of these multiplied values is placed where the sliding window is situated on the output
	- When we perform a stride, the overlapping channel between the two strides is added together (as shown below on the overlapping area)

![[transposed_convolution_ext.png]]

- For a 2D case, we have the below example:
	- Follow the same procedure as above, but now in a 2D situation
	- For the parts over the padding this will obviously be 0, but the area that is on the learnable output parameters will be allocated the multiplication of the input value and the filter value at that location

![[transposed_convolution.png]]

- Overlapping areas are summed up, but like in the 1D case

![[transposed_convolution1.png]]

- This is the final result, with the padding being trimmed for the output

![[transposed_convolution2.png]]

## Practice: 1D example
- The output for this will be [5, 10, 5+8=13, 8, 16, 8]

![[practice-1d-example.png]]

## Why is it Transposed Convolution?
- Look closely at what the below is showing, it is basically what we did in our first example.
- $x,y,z$ acts like our kernel, and the $\overset{\rightarrow}a$ acts as our input
- When we perform the dot product, it essentially acts as just like the output of our above example! But look closely at the difference in dimensions between the left and right
	- When we transpose the matrix, we get a larger output.
	- This is the logic of a transpose convolution

![[why-is-it-transposed-convolution.png]]

![[why-is-it-transposed-convolution1.png]]

## Parallel Patch Classification
- Solution: use encoder-decoder structure
- Encoder **downsample** image, decoder **upsamples*

![[parallel-patch-classification-encoder-decoder.png]]

## 1x1 Convolution Layer
- Convolutional layer with a 1x1 kernel
- Commonly found as last layer(s) of a fully-connected network

![[1x1-conv-layer.png]]

## Loss Function
- At **each pixel**, compute cross-entropy loss between predicted class and known classes

$$
E = - \frac{1}{N} \sum^N_{i=1}y_i \log(\hat{y}_i)
$$

![[loss_function-1x1-convlayer.png]]

## Segmentation with FCN Results
- What the below images are showing that for a stride of 32 we have very blobby segmentation, but as we decrease the stride length we get segments that look closer to the ground truth

![[fcn_results.png]]

![[fcn_results1.png]]

## Problem About FCN
- In the bottleneck of the FCN, how does the decoder _really_ know the proper segmentation when there was lots of averaging during the downsampling going on.
- This is something that U-Net tries to solve

# U-Net

## U-Net
- Originally proposed for medical image segmentation
- Encoder-decoder structure with some additional features

![[u-net.png]]

## U-Net Architecture
1. We turn our image into a 1D vector
2. We have some conv blocks to process our image
3. We then do some strided conv operations to downsample
4. The bottom part is 1x1 conv operations
5. We then do a transposed conv on the beginning of the right-hand side operations to upsample the outputs, but we **also concatenate on the outputs** of the equivalent left-hand side operations as **input for the operation**
6. We repeat this process all the way back up to the top of the right-hand side output (Creating a U-Shape)
7. Finally we get a logits output as our result

- This process allows our model get a lot of context about previous information, so this information isn't lost during downsampling
- The architecture has addition information from the input itself along with the upsampled data

![[u-net-architecture.png]]

## U-Net Architecture
- In decoder, the upsampled feature map is concatenated with the original features from the corresponding encoder layer
- For cell segmentation, U-Net uses only 2 classes and weighted cross-entropy loss: edges between cells have higher weight

![[some-weird-cels.png]]

#  Instance Segmentation

## Instance Segmentation
- Semantic segmentation classifies pixels, doesn't distinguish between instances
- How to separate instances?

![[parallel-patch-classification-encoder-decoder.png]]

## Side Note: Stuff and Things
- Visual scenes include both "things" and "stuff"
	- "Things" = objects, "countable nouns" (cow, person, car) (We count these)
	- "Stuff" regions/materials, "mass nouns (road, grass) (We don't count these)
	- Some classes can be either (trees)
- Instance segmentation (and computer vision in general) mainly focuses on "things"

![[noticing-things.png]]

## Instance Segmentation
- Instead of giving each pixel an instance label, extract patches of image that are likely to be separate instance
- Do segmentation within each patch
- Commonly-used architecture: Mask R-CNN

![[instance-segmentation.png]]

## R-CNN
- R-CNN = Region-based convolutional neural network
- Efficiently extracts "regions of interest" (image patches likely to contain objects) for further processing
- (We'll cover these networks in more detail next week)

## Mask R-CNN
- Mask R-CNN takes patches extracted by R-CNN and runs them through a fully-convolutional network
- FCN predicts a **binary segmentation** mask ("object" or "background")
- Mask R-CNN works by:
	1. Taking a patch from an image from a bounding box and feeds it into a classifying CNN
	 2. It then forms a mask over the area within that patch that it predicts are pixels that belong to a class
	  3. Loss is applied only on the actual true masked area

![[Computer Vision/Images/mask-r-cnn.png]]

## Mask R-CNN Results

![[mask-r-cnn-results.png]]

<iframe width="560" height="315" src="https://www.youtube.com/embed/EfaCtOEEmLA?si=ofMY5zn48RzjmAZY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
## Summary
- Segmentation can be approached in various ways - clustering/graph-based methods vs. pixel classification with FCNs
- Advantages of FCNs
	- Better able to handle very complex objects/backgrounds
	- Likely to give better results for classes on which they are trained
- Disadvantages of FCNs
	- Tend to be worse at capturing precise boundary details
	- May not generalise to classes outside their training set