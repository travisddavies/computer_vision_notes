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

![[instance_segmentation.png]]

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

![[parallel_patch_classification.png]]

## Fully-Convolutional Network
- Fully-connected network (FCN) has only convolutional layers, no fully-connected layers
- Last layer is a spatial map
- Can accept any size image as input, output map size depends on input size

## Parallel Patch Classification
- Standard CNN architecture poses two problems:
	- Receptive field size is linear with the number of convolutional layers
	- Most methods downsample (e.g., with maxpooling) to reduce computation

![[paralllel_patch_classification1.png]]

- Solution: use encoder-decoder structure
- Encoder **downsamples** image, decoder upsamples

![[parallel-patch-classification-encoder-decoder.png]]

## Upsampling
- How to upsample a feature layer in a CNN?

![[upsampling.png]]

## Max Unpooling

![[max_unpooling.png]]

## Convolutional Review

![[convolutional_review.png]]

![[convolutional_review2.png]]

## Transposed Convolution
- Convolution with stride > 1 does a form of downsampling
	- E.g., stride = 2 means filter 2 pixels in input for every 1 pixel in output
	- "Learnable downsampling"
- Can we reverse this to do upsampling?
	- E.g., filter moves 2 pixels in _output_ for every 1 pixel in _input_
 - **Transposed convolution**: convolution with a stride < 1
	- In some papers, may also be called deconvolution, upconvolution, fractionally strided convolution

## Transposed Convolution

![[transposed_convolution.png]]

![[transposed_convolution1.png]]

![[transposed_convolution2.png]]

## Practice: 1D example

![[practice-1d-example.png]]

## Why is it Transposed Convolution?

![[why-is-it-transposed-convolution.png]]

![[why-is-it-transposed-convolution1.png]]

## Parallel Patch Classification
- Solution: use encoder-decoder structure
- Encoder **downsample** image, decoder **upsamples*

![[parallel-patch-classification-encoder-decoder.png]]

## 1x1 Convolution Layer
- Convolutional layer with a 1x1 kernel
- Commonly found as last layer(s) of a fully-connected network
- What do these kernels learn?

![[1x1-conv-layer.png]]

## Loss Function
- At each pixel, compute cross-entropy loss between predicted class and known classes

$$
E = - \frac{1}{N} \sum^N_{i=1}y_i \log(\hat{y}_i)
$$

![[loss_function-1x1-convlayer.png]]

## Segmentation with FCN Results

![[fcn_results.png]]

![[fcn_results1.png]]

# U-Net

## U-Net
- Originally proposed for medical image segmentation
- Encoder-decoder structure with some additional features

![[u-net.png]]

## U-Net Architecture

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
	- "Things" = objects, "countable nouns" (cow, person, car)
	- "Stuff" regions/materials, "mass nounds (road, grass)
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
- FCN predicts a binary segmentation mask ("object" or "background")

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