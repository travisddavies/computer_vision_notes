# Light, Colour & Shadow
- Computer vision scientists often do the opposite of a computer game graphics programmer.
- Computer game graphics aims to replicate the effects of lights reflecting off surfaces for the camera to represent, computer vision scientists aim to reverse engineer this process to find what makes the light reflect off the object in such a way that it gives us a certain effect.
- Take a look at the video below, we can see closely that the light is slightly different on one side of the cube compared to the other because of the light.
	- What's more, the representation of light has formed an illusion to make us think that this is a 3D object!
 - The taking is, by representing subtle contrasts in lighting, we can trick people to think that 2D objects are 3D

<iframe width="560" height="315" src="https://www.youtube.com/embed/tBNHPk-Lnkk?si=NAH1lRD4SOgldVIJ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Learning Outcomes
- Explain the diffuse reflectance model
- Explain how trichromatic colour values are computed and implement colour space transforms
- Explain the problems involved in recreating surface properties from a single image

# Image Formation
## Image Formation Model
- How light is represented is much like the image formation model that we learnt about in [Imaging Basics](imaging_basics.md), as shown below. Light is reflected off a surface and then captured by the camera's sensor plane.

![[image_formation_model1.png]]

## Diffuse (Lambertian) Reflectance
- The intensity of light (i.e. the pixel value of a colour channel) is represented by 4 factors:
	- Light source intensity $I_L$: The intensity of the light (this is a constant)
	- The reflectance $R$: This is a property of the surface that tells how much different colours will be reflected, i.e. the colour that we perceive it
	- Surface's normal vector $N(x)$: This is the angle of the surface that the light is being reflected off
	- Vector of light source $L$: This is the angle of the light coming from the light source
- A dot product of $L$ and $N(x)$ gives us the vector of the reflected light

![[diffuse_reflectance.png]]

## Goal of Vision
- The goal of computer vision is largely to learn the reflectance (the colour) and the surface normal of the object in focus using the provided formula.

![[Computer Vision/Images/goal_of_vision.png]]

# Colour

## Visible Light
- We can represent all colours as RGB.
- This is because, as shown below, colour that we can view will have a spectrum of colours that it can be perceived as, depending on its energy.

![[visible_light.png]]

- Spectral power distribution (SPD) = relative amount of each wavelength reflected by a surface (or produced by a light source)
- Pay attention to the graph, this is basically showing how much of certain colours are present in a light source. 
- This is because white light, as seen from the sun and lights, holds a wide spectrum of different colours.

![[visible_light1.png]]

## Perceived Colour
- Human colour perception is based on 3 types of colour-sensitive cells (cones)
- As shown in the figure below, our eyes are very responsive to red, green and blue!
- Therefore it makes sense to focus on these three colours in computer vision

![[perceived_colour.png]]

- Standard cameras also have 3 colours sensors, each with a different spectral sensitivity

![[perceived_colour2.png]]

- Most surfaces reflect a range of wavelengths, but perceived colour is a function of cone response
- Pay attention to the figure below, each of these right objects are reflecting a range of different colours represented as various wavelengths. 
- We can therefore consider everyday objects as a spectrum of various wavelengths when discussing their colours

![[perceived_lenth3.png]]

- As shown below, we can have a person look at a real lettuce and a picture of a printed lettuce, each with very different spectrum of colours. However since our sensors perceive it in the same way, we can't really tell much of the difference.
- Result: many different spectra appear to be the same colour

![[perceived_colour4.png]]

## Trichromatic Colour Response
- Sensor response = sensitivity x spectrum, integrated over all wavelengths
- Note that **sensor response** equates to the intensity of the colour that we see

![[trichromatic_colour_response.png]]

## Colour Representation
- Common colour spaces:
- RGB (red, green, blue)
	- Most common spaces for digital images
- HSL/HSV (hue, saturation, lightness/value)
	- Attempt to match human understanding of colour
- CIE 1931 XYZ
	- Based on human cone sensitivity, basis for other spaces
- LAB (luminance, a *=red/green, b *=blue/yellow)
	- Approximately perceptually uniform space
 - Below shows a representation of the various colour spaces bounds for all visible colour in the real world
 
![[colour_representation.png]]

## Colour Transforms
- Converting between colour spaces is straightforward:
	- Linear R, G, B values
	- Linear transform, e.g. $\begin{bmatrix}   X \\ Y \\ Z \end{bmatrix} = M\begin{bmatrix}   R \\ G \\ B \end{bmatrix}$
	- Values of $M$ can be looked up for various colour spaces and white points (= the value defined to be "white" for a given colour space)
- Built-in functions in OpenCV, scikit-image

## Example: Colour Swap

![[colour_swap.png]]

# Shading and Surfaces

## Goal of Vision
- The next goal is to look at the surface normal of the object and the light source vector.
- By finding these two parameters, we can learn about the 3D shape of the object's surface.

![[diffuse_reflectance.png]]

## Recovering Surface Normal
- Assume no changes in surface colour/reflectance (constant albedo)
- Can you recover surface normal from image?
- $I_D(x) = N(x) \cdot L = \cos \theta_x$

![[recovering_surface_normal.png]]

- Can recover _angle_ between **surface normal** and **light source**, but not **normal**
- Rather, we can receive a _family of normals_, where it's unclear where which one is the true normal on the given point of the surface
- Below shows an example of this, where the the light is perfectly reflecting off the surface (as shown by the super bright parts) is very easy to find the normal. For the parts along the sides where the surface normal is complex and variable, it is difficult to get these normals.
- However, can add additional assumptions:
	- Normals along boundary of object are known (shown by the green arrows on the edge of the object)
	- Neighbouring normal are similar (i.e. regions should be similar in nearby areas of surface)

![[recovering_normal_surfaces1.png]]

## "Shape from Shading"
- Recover 3D shape from 2D image based only on surface brightness (shading)
	- Note the clear discontinuities of the shape for the person, there are many errors in its reconstruction
- Requires additional assumptions, no algorithm works for all cases
- How does the human visual system do it?
	- Basically the same, but we have good heuristics for our assumptions, this is demonstrated below with lighting priors

![[shape_from_shading.png]]

## Lighting Priors
- One type of prior that our brain uses to reconstruct an image based on light is lighting.
- Look at the two sides in the image below, we would naturally think that the left hand circles are going inside and the right hand circles are going outside
- This is showing us that us humans make assumption that the light is coming from above - as we normally assume that the sun and ceiling lights are above us

![[lighting_priors.png]]

- In this example, since the darker shade is on the sides, it looks more confusing to us.
- You can turn your head to either the left or right and suddenly it'll have the same effect as the above image

![[lighting_priors1.png]]

## Shape Priors
- Another typical prior that humans have is a shape prior. This basically means that we rely on prior knowledge of shapes that we have experienced before to shape our judgement of how we see objects in the real world
- An example below shows how this prior can be easily tricked in humans, as we assumed that the shape was sticking out but it was really sticking in since we know what human faces should look like thanks to prior knowledge

<iframe width="560" height="315" src="https://www.youtube.com/embed/pH9dAbPOR6M?si=AXXZRis-OFGGbapN" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## "Shape from Shading"
- Recover 3D shape from 2D image based only on surface brightness (shading)
- Requires additional assumptions, no algorithm works for all cases
- What if surface isn't constant albedo?

## Recovering Surface Reflectance
- $Luminance = Reflectance \times Illumination$

![[Computer Vision/Images/recovering_surface_reflectance.png]]

## Recovering Surface Reflectance
- This is again another good example of the effectiveness of the human brain to separating out reflectance from surface properties
- We obviously see A and B as different colours, however this is just an illusion thanks to our priors.

![[recovering_surface_reflectance1.png]]

![[recovering_surface_reflectance2.png]]
## Global Shape and Context
- This is another example of our human brain using prior knowledge of global shapes and the contextual information of the image to assume that the diamonds at the top and bottom are different colours.
- We even see the connecting line as changing colour because our visual system is so strong with its use of priors

![[global_shape_and_context.png]]

![[global_shape_and_context2.png]]
## Reflectance from Frequency
- So far we haven't been able to get a computer to have this same visual capabilities like humans, but there are some approaches made to try and replicate it
- Simple approach: assume illumination variation produces low-spatial-frequency changes in image, remove illumination in frequency domain
	- What this means is if the image is blurry (i.e. low-spatial-frequency change), this is likely due to lighting. If there was a high-spatial-frequency change, it was probably a change in colour
 - The formula for this is:
- $L = R \times I$
	- This is just from the original light intensity formula shown at the top of the page
- We convert this into logs so we can sum them:
- $\ln(L) = \ln(R) + \ln(I)$ 
- We can then get the Fourier transform of these:
- $FT(\ln(L)) = FT(\ln(R)) + FT(ln(I))$ 
- Then apply a high-pass filter $g$ in the frequency domain
- $Image = e^{FT^{-1}(g \times FT(\ln(L)))}$

## Reflectance from Frequency
- Below shows a demonstration of the notion from the above formula, where we separate the illumination and the reflectance out of the image
- Separating reflectance and illumination in the frequency domain:

![[reflectance_from_frequency.png]]

- Some limitations of this assumption is if we have hard shadows from corners, this model will consider it part of the surface's colour rather than a change in illumination.
- Shiny objects will not work well with assumptions of high and low spatial frequency change representations since this property is not considered.

## Recovering Surface Properties
- Simple approach: assume lighting is blurry/smooth and hard edges are always due to reflectance
	- Some reflectance edges are smooth
	- Some lighting edges are not smooth (textures, corners)
- Even more complicated in practice!
	- Lighting usually isn't uniform
	- Most surfaces aren't matte/Lambertian

## Cast Shadows
- Another edge case that would fail for our model of extracting surface shape with changes in illumination.
- In our formula of light intensity shown at the top of the page, we make the assumption that Illumination is constant, but as shown by the cast shadow, the illumination for this surface is not actually constant and is varying across the statue.
- This would therefore be modelled incorrectly with our current assumptions.

![[cast_shadows.png]]

## Specularity
- Another properties with surfaces that are not accommodated for in our current model, showing how complicated it is to extract the true shape of objects from images

![[specularity.png]]

## Anisotropy
- Yet another property of images that shows how complicated this problem is to solve.

![[anisotropy.png]]

## Transparency / Translucency
- Yet another to consider - very complicated problem

![[transparency_translucency.png]]