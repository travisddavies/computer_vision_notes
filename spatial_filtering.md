# Spacial Filtering

## Learning Outcomes
- Explain the convolution and cross-correlation operations
- Identify commonly-used filters and their expected outputs
- Explain practical considerations in implementing filters (efficiency, border handling)

# Introduction to Convolution
## Pixel Operator
- Pixel operator: computes an output value at each pixel location, based on their input pixel value

$$
\underset{\text{Output image } g}{g(i,j)} = \underset{\text{Input image} f }{h(f(i,j))}
$$

- Example: $g(i,j) = 0.5(f(i,j))$

![[pixel_operator.png]]

## Gamma Correction

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

## Linear Filtering
- Output pixel's value is a weighted sum of a neighbourhood around the input pixel

$$
\underset{\text{Output image } g}{g(i,j)} = \underset{\text{Kernel } h}{h(u,v)} *\underset{\text{Input image } f}{f(i,j)}
$$

- In this case, $*$ means the convolutional operator

$$
g(i,j) = \sum_{u,v}f(i-u,j-v)h(u,v)
$$

## Linear Filtering
- Consider a $3 \times 4$ image and $2 \times 2$ kernel

![[linear_filtering_1.png]]

![[linear_filtering2.png]]

![[linear_filtering3.png]]

![[linear_filtering5.png]]

![[linear_filtering6.png]]

![[linear_filtering7.png]]

## Cross-Correlation vs. Convolutional

![[cross_correlation_v_convolutional.png]]

**Cross-correlation**: Overlay filter on image

![[cross-correlation-v-convolutional.png]]

**Convolution**: Flip filter horizontally and vertically

# Common Filters
## Practice with Linear Filters

![[practice_with_linear_filters.png]]

![[practice_with_linear_filters2.png]]

![[practice_with_linear_filters3.png]]

## Common Filters: Gaussian

![[common_filters_gaussian.png]]

## Blur Filters

![[blur_filters.png]]

## Common Filters: Sobel

![[common_filters_sobel.png]]

![[common_filters_sobel2.png]]

## What About Colour?
- Consider a 3x4x3 image and 2x2x3 kernel

![[what_about_colour.png]]

Convolutional output?

## Filter Size vs. Filter Support

![[filter_size_v_filter_support.png]]

## Designing Filters

- How could you detect diagonal edges?

![[designing_filtrs.png]]

- How could you simulate (linear) motion blur?

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

![[separable_filters.png]]

## Convolution Output Size
- Valid convolution: the output image is smaller than the input image
- Why?

![[convolution_output_size.png]]

## Border Handling
- Pad with constant value

![[border_handling.png]]

- Wrap image

![[wrap_iamge.png]]

- Clamp / replicate the border value

![[clamp_border.png]]

- Reflect image

![[reflect_image.png]]

## Practical Considerations
- Think about how to implement filters efficiently
	- Images are big, so efficient filtering can save a lot of time!
- Think about how to handle borders
	- No one-size-fits-all solution
	- Wrap is ideal for tiling textures (but not photos)
	- Clamp/replicate tends to work well for photos