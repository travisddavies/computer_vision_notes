# Image Formation
## Learning Outcomes
- Use the pinhole camera projection model to map between world and image points
- Explain how camera parameters affect the appearance of an image
- Explain how images are represented in a computer as 3D tensors
- Explain why resampling is necessary for image manipulators, and the trade-offs of different methods

## What is an Image?
- Pattern formed by light falling on a photosensitive surface
	- Examples of photosensitive surfaces = camera sensor, retina
- Light is reflected off of objects in the world
- 2D projection of a 3D scene
- Take a look at the below image. What is basically happening is we have an outside world being projected upside-down onto the wall of a dark room through a tiny hole. This is basically how a pinhole camera works.

![[eye_and_nature_of_vision.png]]

## Creating an Image
- Simple imaging system: pinhole camera or "camera obscura"
- Much like our example in the above image, here is an example of a tree being projected in a pinhole camera.

![[upside_down_photo.png]]

- Every point in the image corresponds to a point in the world
- Take note of how the coordinate system works for a pinhole camera:
	- The origin $(0,0,0)$: The **pinhole** of the camera, or "aperture"
	- $z$: The distance between object and the camera
	- $f$: The **focal length**, or the distance between the pinhole and the **imaging surface**
	- $h$: The height of the **imaging surface** of the camera
	- $C$: The point on the object that points perpendicular to the camera
	- $P$ $(x,y,z)$: A point on the object that is captured by the pinhole camera
	- $x$ and $y$: Coordinates on the face of the object that are captured by the pinhole camera
	- $C_i, P_i, x_i, y_i$: The equivalent points from the object but in the pinhole camera space
- What the equation shows is that the coordinate space in the real world is relational to the coordinates in the pinhole camera.
	- As $f$ grows, so does the size of the object in the pinhole camera
	- As $z$ grows, the object shrinks in the pinhole camera
 
![[distance_diagram.png]]

- Pinhole camera: simple design, not common in practice
	- Why not?
		- Because these types of cameras require a very sensitive film, and we can have clearer images using other techniques
  
![[pinhole.png]]

- Instead of a pinhole, most cameras use lenses
	- Why?
		- Because as shown below, as we let in more light, one point on a surface now illuminates a lot of the imaging surface, and multiple points will now overlap, causing a blur. Therefore we require lenses to bend the light.
  
![[opened_pinhole.png]]

## Lenses
- Lenses focus light rays onto a single point ($F$) at a distance ($f$) beyond the lens
- Aperture diameter ($D$) restricts the range of rays
- This technique is to mitigate the issue that we explained above about the blur caused from a large aperture in the pinhole camera. This is done by:
	- Creating a lens that focuses a ray of light in the world to a certain point in the camera, the **focal point** $F$. This allows us to focus on objects far away without blur
	- Also note the focal length $f$, as with digital cameras, we can adjust our focus of objects in the real world by adjusting how much our lens protrudes out of the camera, which is basically just adjusting $f$!
		- The **larger** the focal length $f$, the the narrower angle of view. This means narrower view of the world and more focus on further objects. 
		- The **smaller** the focal length $f$, the wider the angle of view. This means they capture a wider view of the world.

![[lense.png]]

- Another example is shown below. Take the example below, what we have is an image plane line in the pinhole example, and an object that is in focus on the right hand side. As can be seen from the angles of the light from the lens. We will get a very clear image of this particular object.

![[depth_of_field1.png]]

- Now let's look at a point that's closer than the point of focus. As can be seen by the fact that it has a spread over the image plane, this object will be blurred in the actual image.

![[depth_of_field2.png]]

- The same for an object that is further away, it will also cause a spread on the image plane.
- The **depth of field** in this case, we say is the distance of these two out-of-focus points, the two objects closest and furthest that can be in focus with the given focal length

![[depth_of_field3.png]]

## Focal Length / Angle of View
- Focal length is the basic description of a photographic lens, specifically a calculation of an optical distance from the point where light rays converge to form a sharp image of an object to the digital sensor at the focal plane in the image. 
- Lens focal length tells us about the **angle of view** - how much of the scene will be captured - and **magnification** - how large individual elements will be. 
	- The **longer** the focal length, the **narrower** the angle of view and the **higher the magnification**.
	- The **shorter** the focal length, the wider the angle of view and the **lower the magnification**
- Take for example the image below:
	- The first image captures more of the wall, but the distant bottle looks very small
	- The third image captures less of the wall, but the distant bottle looks larger

![[focal_length.png]]

Answers:
- 1 is C
- 2 is B
- 3 is A

## Focal Length / Angle of View
- Again, we can see that the larger the focal length of the lens, the narrower the field of view but the more magnification we get

![[focal_lengths.png]]

## Depth of Field
- In cameras, aperture size controls depth of field (smaller aperture = greater range of depth in focus)
- Wider aperture focuses on closer objects, narrower aperture also gives more focus to distant objects

![[Computer Vision/Images/depth_of_field.png]]

- However, one drawback is that we are letting in less light, and consequently making the image darker
- One example is shown below, when our aperture is larger, the closer painting is more in focus and the image is much brighter

![[depth-of-field4.png]]

- Smaller aperture is clearer for each painting now, but it is now much darker

![[depth-of-field6.png]]
## Distortion
- Distortion is when straight lines in the real world look curved in an image. 
- This is when the focal length of a lens is so small that it bends the light outwards so much that it makes the world look like it's bending inwards
- This is known as barrel distortion

![[las_vegas.png]]

## Image Formation Model
- The figure below gives a simple explanation of image formation.
- The light from a source is reflected on a particular surface.
- A part of that reflected light goes through an image plan that reaches a sensor plane via optics
- Given that the image on the sensor plane will be upside down, we can extrapolate the image for calculations by having the image plane at the same distance from the optics as the sensor plane, and scaled accordingly.

![[image_formation.png]]

## Image Formation
- World parameters
	- Light source
	- Surface properties
- Camera parameters
	- Focal length / angle of view
	- Aperture size / depth of field
	- Lens distortion

# Digital Images
## What is a (Digital) Image?
- A tensor (3D array of values)
	- $Width \times height \times channel$
	- 3 channels = RGB colour image (red, green, blue)
	- 1 channel = grayscale image
 - Note: the exact format can vary across libraries / languages!
	 - E.g., $\text{"channel-first"} = channel \times height \times width$ 
	 - $Height \times width \times channel$
	 - $BRG = blue, red, green$ 
  
![[3D_tensor.png]]

### Colour channels
- Below shows what the colours look individually in the 3 colour channels, the redness, greenness and blueness of the channel corresponds to the brightness of the image.

![[colours.png]]

- Pixel = smallest unit of an image
	- Grayscale: pixel is a grayscale value
	- Colour image: pixel is a 1x3 vector
 
![[pixel.png]]

- Most common data type is unit8 (unsigned 8-bit integers)
	- Range = 0 - 255
	- "24-bit colour" = 3 uint8 channels
- But you may encounter other data types:
	- Double (range 0.0 - 1.0)
	- uint16, uint32: medical images
	- float32: high dynamic range (HDR) images
 
## Common File Formats 
- Lossy compression:
	- JPEG (.jpg, .jpeg)
- Lossless compression:
	- PNG (.png), BMP (.bmp), GIF (.gif), TIF (.tif, .tiff)
 
![[melb_144kb_compressed.png]]

## JPEG Compression

![[Computer Vision/Images/jpeg_compression.png]]

## Digital Images
- Stored as a tensor (3D array) of values
- Colour is represented through multiple colour channels (typically red, green, blue = RGB)
- Values are typically uint8 (0-255)
- Some image formats discard information to save space (lossy compression)

# Image Manipulation
## Image Scaling
- For the beginning of our journey manipulating images, we can discuss image scaling.
- To downsize an image, there are various different ways that this is done, with different results.
- One can be to crop an image, as shown in the image below, which basically chops off the ends of the image
- Another is resizing, which will remove a certain amount of pixels throughout the image, dependent on scale.
	- As can be seen, the result can lead to images where the aspect ratio is or is not preserved

![[image_scaling.png]]

- In the picture below, we can see an example of resampling, however this one is jagged and pushed into the upper left corner of the picture.

![[image_scaling2.png]]

![[bad_resampling.png]]

- This picture below, however, has resampled the image by averaging over every 4 pixels. It's preserved the shape of the original image, but this operation is much more costly as it calculates for pixels for every pixel

![[image_scaling3.png]]

![[average_resampling.png]]

# Resampling Methods
- Nearest-neighbour: closest value to sample point (as shown with the first example above)
	- Simple, preserves hard edges
	- Smooth curves may be blocky/distorted
- Bilinear: weighted average of 4 pixels around sample point (as shown with the second example above)
	- Smoother curves, but blurs hard edges
	- Slower to compute
- Other options: bicubic, Lanczos

![[resampling.png]]

## Image Resampling
- The results for these two resampling methods are shown below
	- As can be seen, nearest neighbour looks much more blocky, while bilinear looks much smoother

![[image_resampling.png]]

![[resampling techniques.png]]

## Image Manipulations
- Crop = extract a subset of the image array (doesn't require resampling)
- Resize = change the dimensions of the image array (requires resampling)
- Different resampling methods give different results