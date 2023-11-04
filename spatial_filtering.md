# Spacial Filtering

## Learning Outcomes
- Explain the convolution and cross-correlation operations
- Identify commonly-used filters and their expected outputs
- Explain practical considerations in implementing filters (efficiency, border handling)

# Introduction to Convolution
## Pixel Operator
- Pixel operator: computes an output value at each pixel location, based on their input pixel value
- This is defined as just the following function:

$$
\underset{\text{Output image } g}{g(i,j)} = \underset{\text{Input image} f }{h(f(i,j))}
$$

- Example: $g(i,j) = 0.5(f(i,j))$

![[pixel_operator.png]]

 - This is just a simple function on one pixel, and is very limiting because we don't have any information about our local surroundings. This is where local operators come in

## Gamma Correction
- Another example is shown below, basically we can change the value of the pixels so that the histogram for the pixel value goes brighter or darker. 
- As can be seen in the second image:
	- Pushing the pixels to the right end of the histogram makes the image brighter
	- Pushing the pixels to the left end of the histogram makes the image darker

![[gamma_correction.png]]

## Local Operator
- Local operator: computes an output value at each pixel location, based on a neighbourhood of pixels around the input pixel
- Example: sharpening filter

![[local_operator.png]]

## Linear Filtering
- Output pixel's value is a weighted sum of a neighbourhood around the input pixel

$$
\underset{\text{Output image } g}{g(i,j)} = \underset{\text{Kernel } h}{h(u,v)} \otimes \underset{\text{Input image } f}{f(i,j)}
$$

- In this case, $\otimes$ means the **cross-correlation convolution**

$$
g(i,j) = \sum_{u,v}f(i+u,j+v)h(u,v)
$$

- This above formula basically means:
	- Treat the kernel like a sliding window of $m \times n$ dimensions, and slide it over the image from top to bottom, left to right
	- We multiply each corresponding pixel in the image with the corresponding pixel in the kernel 
	- We then add up all the multiplications and assign that value to the central pixel that the kernel is placed on.
 
## Linear Filtering
- Output pixel's value is a weighted sum of a neighbourhood around the input pixel

$$
\underset{\text{Output image } g}{g(i,j)} = \underset{\text{Kernel } h}{h(u,v)} *\underset{\text{Input image } f}{f(i,j)}
$$

- In this case, $*$ means the convolutional operator

$$
g(i,j) = \sum_{u,v}f(i-u,j-v)h(u,v)
$$

- What this basically means is that we do the exact same thing as for the **cross-correlation** operation, except we would multiply the top-left pixel in the image with the bottom-right pixel in the kernel, and follow that pattern for the whole operation.
	- Multiply bottom pixel of image with top pixel of kernel
	- Multiply left pixel of image with right pixel of kernel
	- Follow same pattern throughout

## Linear Filtering
- Consider a $3 \times 4$ image and $2 \times 2$ kernel
- We will no show how the linear operations for cross-correlation is performed

![[linear_filtering_1.png]]

- We slide one pixel at a time and continue the operations

![[linear_filtering2.png]]

![[linear_filtering3.png]]

![[linear_filtering5.png]]

![[linear_filtering6.png]]

- And voila, we have performed the cross-correlation operation

![[linear_filtering7.png]]

## Cross-Correlation vs. Convolutional
- A visual demonstration of how the cross-correlation and convolutional operations are different is shown below
- Matching pixels where the kernel is placed over the image will be multiplied together, as shown in the below result

**Cross-correlation**: Overlay filter on image

![[cross_correlation_v_convolutional.png]]

- For convolutional, it's the other way around, so the result is actually the opposite, as shown below

**Convolution**: Flip filter horizontally and vertically

![[cross-correlation-v-convolutional.png]]


# Common Filters
## Practice with Linear Filters
- One example for applying linear filters is shown below.
- For this example, it is the equivalent of applying no change.
- This is because all neighbouring pixels will be multiplied zero, and the central pixel value will just remain the same.

![[practice_with_linear_filters.png]]

- If we have just one pixel with the value of one on the right, we will shift everything to the left.
- This is because you're making every pixel take its neighbouring pixel value on the right, thus shifting it to the left.

![[practice_with_linear_filters2.png]]

- If we accentuate the central pixel and then subtract it my the average of the neighbouring pixels, as shown below, we will sharpen the image.
- This is because you're applying a higher weight to the differences in the pixel values with its neighbours.

![[practice_with_linear_filters3.png]]

## Common Filters: Gaussian
- One common filter we can use is the gaussian filter. This will basically give a smooth weighted average of sorts for neighbouring pixels, were the closest neighbours are weighted higher and then further out are weighted exponentially less.
- For a 5x5 kernel, we can simplify this by making a kernel like shown on the right

![[common_filters_gaussian.png]]

## Blur Filters
- As shown in the images below, the gaussian filter gives a somewhat smoother effect compared to just a simple averaging of neighbouring pixels. 
- Average kernel gives weird streaks as shown below

![[blur_filters.png]]

## Common Filters: Sobel
- Sobel filter acts as the derivative of the gaussian blur, which actually accentuates the edges in the image.
- The reason why we apply more weight on the centre parts is because it acts as a combination of blurring and sharpening, and ultimately gives a better result.
- As can be seen, when the sobel filter is applied vertically on the kernel, it emphasises the vertical edges
- When the sobel filter is applied horizontally on the kernel, it emphasises the horizontal edges

![[common_filters_sobel.png]]

![[common_filters_sobel2.png]]

## What About Colour?
- Consider a 3x4x3 image and 2x2x3 kernel
- We set up a kernel for each channel to perform our convolutional operations, as shown below
- For example, we could apply 0 to green, 0 to blue and 1 to red and we will get just the red channel as the output

![[what_about_colour.png]]

## Filter Size vs. Filter Support
- Despite having kernels of the same size, varying filter support gives a different output
- As shown below, having a 2 kernels of same size, but smaller $\sigma$ gives a smaller blur, while a larger $\sigma$ gives a much larger blur. 
- This makes sense, as a weighted average for distant neighbours will also be applied to the second filtered image, whereas just nearby neighbours will get a weighted average for the first image

![[filter_size_v_filter_support.png]]

## Designing Filters
- How could you detect diagonal edges?
	- Rotate the image 45 degrees and then apply Sobel filter

![[designing_filtrs.png]]

- How could you simulate (linear) motion blur?
	- A gaussian blur but only have values in the centre row, possibly 10 wide.

![[motion_blur.png]]

## Common Filters
- Average/blur filters: average pixel values, blur the image
- Sharpening filters: subtract pixel from surround, increase fine detail
- Edge filters: compute difference between pixels, detect oriented edges in image

# Filters in Practice
- Commutative: $f * h = h * f$
	- Theoretically, no difference between kernel and image
	- But most implementations do care about order
- Associative: $(f * h1) * h2 = f * (h1 * h2)$
	- Usually one option is faster than the other - allows for more efficient implementations
- Distributive over addition
	- $f * (h1 + h2) = (f * h1)  + (f * h2)$
- Multiplication cancels out
	- $kf * h = f * kh = k(f * h)$

## Efficient Filtering
- Multiple filters: generally more efficient to combine 2D filters $(h1 * h2 * h3...)$ and filter image just once

![[efficient_filtering.png]]

- Separable filters: generally more efficient to filter with two 1D filters than one 2D filter
- For example, the 2D Gaussian can be expressed as a product of two 1D Gaussians (in $x$ and $y$)

$$
G_{\sigma}(x,y) = \frac{1}{2 \pi \sigma^2}\exp^{-\frac{x^2+y^2}{2\sigma^2}}
$$

$$
G_{\sigma}(x,y) = \Bigg(\frac{1}{\sqrt{2 \pi \sigma}} \exp^{-\frac{x^2}{2\sigma^2}}\Bigg)\Bigg(\frac{1}{\sqrt{2\pi\sigma}} \exp^{-\frac{y^2}{2\sigma^2}}\Bigg)
$$

## Separable Filters
- This is just showing the above demonstration for the properties of filters but in the application of a Gaussian kernel.

![[separable_filters.png]]

## Convolution Output Size
- Valid convolution: the output image is smaller than the input image
- Why?
	- Because as we apply our convolutional operations over the matrix, each kernel operation becomes one single pixel and thus reduces the size of the output.
- This can sometimes be unwanted, and we can do things to manage this, by doing border handling

![[convolution_output_size.png]]

## Border Handling
### Pad with constant value
- We just add a border of zeros around the image so that our image remains the same size.
- Introduces blackness on the edges, as zero pixel values are the colour black

![[border_handling.png]]

### Wrap Image
- We can wrap the image by putting the edge of one side to be the border on the other side, as shown below.
- This gives a bit more blending, but it gives a somewhat unwanted blur effect on the edges because of these wrapped borders

![[wrap_iamge.png]]

### Clamp / Replicate the Border Value
- We can also just expand the colour on the edges outwards
- This often gives the best effect

![[clamp_border.png]]

### Reflect Image
- Similar to clamp, except applying an algorithm to use the same pattern on the edges of the image

![[reflect_image.png]]

## Practical Considerations
- Think about how to implement filters efficiently
	- Images are big, so efficient filtering can save a lot of time!
- Think about how to handle borders
	- No one-size-fits-all solution
	- Wrap is ideal for tiling textures (but not photos)
	- Clamp/replicate tends to work well for photos