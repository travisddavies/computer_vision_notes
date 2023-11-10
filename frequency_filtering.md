# Frequency Filtering
## Adversarial Images
- Below we have two images that look identical to us, but for a neural net they look different.
- This is because the frequency has been changed in the second image with an adversarial neural net.
- Take a look at the tongue of the second image, you can see that the pixel values have been slightly changed. To us this seems like nothing, but not for a neural net.

![[adversarial_images.png]]

## Learning Outcomes
- Explain at a conceptual level how images are represented in the frequency domain
- Implement filters in the frequency domain
- Explain how frequency representations are used for image compression and analysis

# Fourier Analysis (in 2D)
## Signals
- Any signal or pattern can be described as a sum of sinusoids
- This can be seen in the two diagrams below, for the first one, if we cut the image like a cake down the red line, we will get a wave value like shown below the image where the height is represented by brightness
- The right image shows a wave representation of the image too
- Any image can therefore be represented as a sinusoidal wave of signals

![[signals.png]]

![[signals2.png]]

## Sinusoids
- Below shows a sinusoidal wave, the basis of how we can represent an image as a frequency.

![[sinusoids.png]]

- The general parameters we can use to manipulate this wave are shown below:
	- $A$ will influence the height of the wave
	- $\omega$ will influence the frequency of the wave
	- $\psi$ will influence the phase, i.e. where the beginning of the wave starts on the $x$ axis  

![[sinusoids2.png]]

- Below shows a standard wave: $A=1, \omega=1, \psi=0$

![[sinusoids3.png]]

- This shows when we double $A$

![[sinusoids4.png]]

- This shows when we triple $A$

![[sinusoids6.png]]

- This shows a combination of altering $A$ and $\omega$

![[sinusoids7.png]]

- This shows a combination of altering $\psi, A, \omega$

![[sinusoids9.png]]

## Fourier Analysis
- Any signal can be expressed as a sum of sinusoids
- For example, if we want to create a square wave, we could simply do a summation of the below waves to a certain frequency and amplitude and we will eventually be able to replicate the wave

![[fourier_analysis.png]]

## Sum of Sinusoids
- Below displays the above statement better.
- Notice that as we keep adding the waves, we slowly get close to the square wave
- So we need a lot of different waves of varying frequencies, amplitudes and phases to represent the square wave
- This is a good example of the **Fourier Transform**!

![[sum_of_sinusoids.png]]

![[sum_of_sinusoids2.png]]

![[sum_of_sinusoids3.png]]

## Fourier Transform
- Fourier transform decomposes signal into component frequencies
	- Values are complex numbers representing amplitude and phase of sinusoids
	- Time domain $\rightarrow$ frequency domain (or, for images, spatial domain $\rightarrow$ frequency domain)
- $F(\omega) = \int^{\infty}_{-\infty} f(x)\exp^{-2i \pi \omega x}dx$ 
- scipy.fft(1D), scipy.fft2(2D), scipy.fftn(3D+)
- Inverse Fourier transform converts from frequency domain back to space domain

## Frequency Spectrum
- Below shows a representation of the left wave. 
- The height of the bars represents the amplitude $A$ and the $x$ axis represents the frequency $\omega$

![[frequency_spectrum.png]]

- The zero frequency represents the mean amplitude

![[frequency_spectrum2.png]]

- Values in frequency domain are complex numbers
- For each **frequency**: magnitude=(**amplitude**) and angle (=**phase**)

![[frequency_spectrum4.png]]

# Fourier Analysis (Images)

## Images as Sinusoids
- Again, if we were to slice through these two images, we would be able to represent the images as these two waves. 
- The height of the waves represents the brightness of the pixels

![[images_as_sinusoids.png]]

## Fourier Transforms of Images
- If we were to take the Fourier transform of these thee images, we will get the below images
- All Fourier transforms in these images are reflected, so the left hand side will always look like the right hand side
- Everywhere that is black represents zero values, except for the 3 peaks in the middle of the plots
- If you compare the first image to the second, we can see that the 3 dots are further apart for the second one. This means that it has a higher frequency.
- Also note that the third image's Fourier transform is rotated, just like the image itself. This means that the Fourier transform goes along the sinusoidal wave

![[Computer Vision/Images/fourier_transform_of_images.png]]

## How to Interpret Fourier Spectra

![[how_to_interpret_fourier_spectra.png]]

## Fourier Transform of Images
- We can do interesting things such as combining two images' Fourier transform, which is what we see below

![[fourier_transform_of_images1.png]]

- Notice how we now have many different peaks in the Fourier transform in the below image. 
- This is because the left image represents a square wave, and therefore has many sinusoidal waves, as represented on the right

![[fourier_transform_of_images2.png]]

- Similar for this below image, but now we have square waves going in all directions

![[fourier_.png]]

- For a real image, we can see:
	- A peak in the middle, representing the mean amplitude of the image
	- Stripes going in diagonal directions, there are because of the various diagonal patterns in the image, e.g. the tripod, the leaning man, the handle, etc.

![[fourier_transform_of_images3.png]]

- Much the same for the Fourier transform of the below dots, it forms a stripy patterns in all directions

![[fourier_transform_of_images4.png]]

## Magnitude and Phase
- The phase and magnitude represent everything about an image
- Below shows a separation of the magnitude and phase of the two images

![[magnitude_and_phase.png]]

- If we combine the phase of the koala and the magnitude of the city, we can see that the representation of the koala is remained in the phase
- This is because the phase retains the information of the edges of the image.
- The texture of the buildings is retained in the image, which is also shown in the combined image, but does look blurry
- Same story for the phase of the city on the magnitude of the koala

![[magnitude_and_phase1.png]]

## Fourier Analysis of Images
- Any image can be represented by its Fourier transform
- Fourier transform = for each frequency, magnitude (amplitude) + phase
- Magnitude captures the holistic "texture" of an image, but the edges are mainly represented by Fourier phase

# Frequency Filtering

## Operations in Frequency Domain
- Operations in the spatial domain have equivalent operations in frequency domain
- Convolution in spatial domain = multiplication in frequency domain

$$
FT[h * f] = FT[h]FT[f]
$$

- Inverse:

$$
FT^{-1}[hf] = FT^{-1}[h] * FT^{-1}[f]
$$

## Bandpass Filter
- Bandpass filter = a filter that removes a range of frequencies from a signal

## Low Pass Filter
- Low pass filter = keep low spatial frequencies, remove high frequencies
- If we then filter out everything far from the peaks in the Fourier transform, we will be actually creating a low pass filter. 
- This is because we are only retaining the lower frequencies of the image and throwing away the higher frequencies.
- This also gives a "ringing effect" because of the circular filter

![[low_pass_filter.png]]

![[low_pass_filter2.png]]

![[low_pass_filter3.png]]

## High Pass Filter
- High pass filter = keep high spatial frequencies, remove low frequencies
- Much the same for the opposite, we are throwing away the lower frequencies and keeping the higher frequencies

![[high_pass_filter.png]]

![[high_pass_filter2.png]]

![[high_pass_filter3.png]]

## Filter Artefacts
- Why does this happen?
	- Because the filter was a hard circle

![[filter_artefacts.png]]

## Inverse Convolution Theorem
- Given the nice properties of the Fourier transform, we can multiply the Fourier transform and the circular filter and it'll give us the weird ripply image
- This is what creates the "ringing" effect in the image

![[inverse_convolution_theorem.png]]

![[inverse_convolution_theorem2.png]]

## Gaussian Low Pass Filter
- If we did a Gaussian shape rather than a hard circular shape, we'll get a much better effect.

![[gaussian_low_pass_filter.png]]

## Gaussian High Pass Filter
- Much the same for the high pass filter

![[gaussian_high_pass_filter.png]]

# Applications

## Image Compression
- Frequency domain is a convenient space for image compression
- Why?
- Human visual system is not very sensitive to contrast in high spatial frequencies
- Discarding information in high spatial frequencies doesn't change the "look" of an image

- JPEG compression: break image into 8x8 pixel blocks, each represented in frequency space
- Discrete cosine transform (DCT)
- High spatial frequency components are quantised

![[image_compresssion.png]]

## JPEG Compression
- The trick of JPEG compression is to break up an image into blocks and then discard the high spatial frequency components through quantising.
- The below shows a spectrum of this technique, the left being overdone and the left being hardly noticeable.

![[Computer Vision/Images/JPEG_compression2.png]]

## Image Forensics
- Below shows the difference of the Fourier transform of the two dog images, the left one being the original image and the left being the adversarial image.
- This may not show much difference to us, but we can show differences via a different way.

![[Computer Vision/Images/image_forensics.png]]

## Dog vs. Hot Dog - What Changed?
- Below shows a much clearer difference in the two images, this is why neural nets notice a difference!

![[dog_v_hotdog_what_changed.png]]

## Image Forensics
- To find which images are fake, we can actually check Fourier spectrum of the images. The left shows the Fourier spectrum of a real image, and the right shows the Fourier spectrum of the fake image. 
- Fake images often leave some evidence behind, including the checkerboard pattern on the right-hand image

![[image_forensics_1.png]]