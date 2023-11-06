# Image Generation

## Learning Outcomes
- Explain the difference between discrimative and generative models
- Explain how regular and variational autoencoders work, and how they differ from each other

# Background: Generative Models

## Probability Notation
- $P(x)$ = probability of an event $x$
- Joint probability: $P(x,y) = P(x \cap y)$ 
	- Probability of both $x$ and $y$ occuring
- Conditional probability: $P(x|y) = \frac{P(x \cap y)}{P(y)}$
	- Probability of $x$ occurring, given $y$

![[probability_notation.png]]

## Probability Distribution
- Probability distribution $P(x)$
	- A function that assigns a non-negative value to each possible $x$ that represents the likelihood of $x$
- Example: Probability distribution for occurance of letters (a-z) in English text

![[probability_distribution.png]]

## Probability Density Function
- Probability density function $P(x)$ for continuous $x$
	- A function that assigns a non-negative value to each possible $x$ that represents the likelihood of $x$
- Example: Probability density function for weekly income in 
  Australia

![[probabiility_density_function.png]]

## Discriminative vs. Generative
- **Discriminative** models
	- Learn conditional probability of class $Y$ given attributes $X: P(y|x)$

![[discriminative_v_generative.png]]

## Discriminative Model
- Input is an image
- Output is a probability density function over labels $P(y|x)$

![[discriminative_model.png]]

![[discriminative_model2.png]]

![[discriminative_model3.png]]

## Discriminative vs. Generative
- Discriminative models
	- Learn conditional probability of class $Y$ given attributes $X: P(y|x)$
- Generative Models
	- Learn joint probability of attributes $X$ and class $Y: P(x,y)$
- Generative model contains discriminative model: you can use the joint probability to get $P(y|s)$
- AND generative can do the reverse: $P(x|y)$

## (Conditional Generative Model)
- Input is a label
- Output is a probability density function over images $P(x|y)$

![[Computer Vision/Images/conditional_generative_model.png]]

## (Unconditional) Generative Model
- Output is a probability distribution $P(x)$
- What is the probability that this is an image

![[unconditional_generative_model.png]]

## Bayes'  Rule
- Relationship between these models:

![[Computer Vision/Images/bayes_rule.png]]

## Generative Models
- Generative models can generate new samples from the learned distribution

![[basket.png]]

- Generative models can generate new samples from the learned distribution

![[generative_models_face.png]]

- Generative models can generate new samples from the learned distribution

![[synthesisied_patterns.png]]

## Autoencoders

## Unsupervised Learning
- Learn a model for unlabelled data
- Goal is to find a model that represents the data as well as possible, usually with fewer parameters
- Uses:
	- Simpler model for another ML system
	- Form of dimensionality reduction
	- Potentially better generalisation


## Unsupervised Learning + NNs
- Like supervised machine learning algorithms, unsupervised algorithms may not work well in "raw" input spaces (text, images)
- Why?
- Solution? Embeddings (e.g., from neural networks might work better)
	- But we have no labels to learn the embeddings for our task
	- Embeddings learned for a different task may not give a complete representation of the data

## Autoencoders
- Essentially, neural networks for unsupervised learning
- Sometimes called "self-supervised" learning
- Output of the network is whatever was passed to the network (e.g., an image)
- Hidden layer learns a lower-dimensional representation of the input

## Basic Autoencoder Architecture

![[basic_autoencoder_architecture.png]]

## Deeper Autoencoder Architecture

![[deeper_autoencoder_architecture.png]]

## Autoencoders
- Encoder/decoder architecture
	- **Encode** in a hidden layer
	- Hidden layer is smaller than the inputs (fewer neurons)
	- **Decode** to an output layer
	- Often the encoding and decoding weights are forced to be the same
- Goal: output the input

## Hidden Layer
- "Bottleneck" layer - smaller than the input
- Represents the input in terms of **latent variables**
- In the simplest case (one hidden layer with linear activation), this layer learns PCA

- Why does this layer need to be smaller than the input?

## Output and Loss
- Unlike a standard NN, the output is not a class or regression value - it's the same type as the input (e.g., an image)
- Activation function is chosen appropriately:
	- For a binary image, tanh or sigmoid
	- For a regular image, linear activation
- Loss function = difference between input and output (e.g., MSE)

## Latent Representation

![[latent_representation.png]]

## Autoencoder
- Unsupervised learning (no labels)
- Learns a latent representation from data: lower-dimensional set of features that explains the data
- Not a true generative model - no way to sample new data
	- You could "sample" by giving random latent variable values to the encoder, but no guarantee that these will produce real images

## Variational Autoencoder
- Probabilistic version of an autoencoder: learn latent representation and sample from the model to generate new images

![[Computer Vision/Images/variational_autoencoder.png]]

- Assume images are generated from some distribution over latent variables $z$
- Assume a simple prior $P(z)$, e.g. uniform or Gaussian distribution

## Variational Autoencoder (VAE)
- Probabilistic version of an autoencoder: learn latent representation and sample from the model to generate new images

![[Computer Vision/Images/variational_autoencoder.png]]

- Probabilistic decoder learns $P(x|z)$
- Probabilistic encoder learns $P(z|x)$
- Goal: maximise the likelihood $P(x)$

## Probabilistic Decoder
- Input: latent variables $z$
- Output: mean $\mu_{x|z}$ and diagonal covariance $\sum_{x|z}$, parameters of a Gaussian distribution that generates $x$ conditional on $z$

![[probabilistic_decoder.png]]

- $P(x|z) = N(\mu_{x|z}, \sum_{x|z})$
- Goal: maximise $P(x) = \frac{P(x|z)P(z)}{P(z|x)}$

## Probabilistic Encoder
- Input: Image $x$
- Output: mean $\mu_{z|x}$ and diagonal covariance $\sum_{z|x}$, a Gaussian distribution over latent variables conditional on $x$

![[probabilistic_encoder.png]]

-  Input: image $x$
- Output:  mean $\mu_{z|x}$ and diagonal covariance $\sum_{z|x}$, a Gaussian distribution over latent variables conditional on $x$

![[probabilistic_encoder.png]]

- Learns approximation of $P(z|x)$: $q(z|x) = N(\mu_{z|x}, \sum_{z|x})$

## Variational Autoencoder (VAE)
- Encoder and decoder are concatenated and trained jointly

![[variational_autoencoder_1.png]]

## Loss Function
- Goal: maximise likelihood $P(x)$
- Loss is based on variational lower bound on $P(x)$:

![[loss_function.png]]

- Loss consists of two terms: reconstruction loss and regularisation loss
- Reconstruction loss: encourages network to create output images as similar as possible to input image
- Regularisation loss: encourages network to learn a latent representation $z$ that is similar to the prior (standard normal distribution)

## Properties of the Latent Space
- To be useful for generation, the latent space should be:
	- Continuous: nearby points in the latent space correspond to similar images
	- Complete: every point in the latent space corresponds to a valid point
- Standard normal distribution satisfies both of these requirements
- Use of diagonal covariance matrices ensures latent variables are independent

## Application: Sampling New Images

![[sampling_new_images.png]]

![[sampling_new_images2.png]]

## Latent Space Visualisation

![[latent_space_visualisation.png]]

## Application: Image Manipulation
- Given the latent-variable representation $z$ of image $x$, can change values of $z$ to create variations on $x$
- Nearby points in latent space correspond to similar images (continuity requirement) and axes are independent
- But directions in latent space may not correspond to recognise image properties (without additional constraints)

![[image_manipulation.png]]

![[image_manipulation2.png]]

## Variational Autoencoder (VAE)
- Advantages
	- Learns approximations of $P(z|x)$ and $P(x|z)$, where $z$ is a latent variable representation of the input $x$
- Disadvantages
	- Outputs often blurry (why?)

## Final Note
- More recent VAEs use better image representations to reduce blur

![[final_note.png]]

