# Generative Adversarial Networks (GANs)

## Learning Outcomes
- Explain the architecture and objective function of a standard GAN
- Explain common pitfalls in GANs, and how GANs can be evaluated
- Explain common architectures for conditional GANs

# GAN Architecture

## (Unconditional) Generative Model
- Output is a probability distribution $P(x)$
- What is the probability that this is an image?

![[unconditonal_generative_model.png]]

## (Conditional) Generative Model
- Input is a label
- Output is a probability density function over images $P(x|y)$

![[conditional_generative_model 1.png]]

## GANs
- **Generative Adversarial Networks** (GANs) are neural networks that learn to generate instances from a particular distribution (e.g., images of faces)
- Actually consist of two neural networks: a **generator** and a **discriminator**
- Training involves a sort of competition between the two networks

## GAN Architectures

![[gan_architectures2.png]]

## Generator
- GAN Generator doesn't actually learn the probability distribution $P(x)$, but learns to sample from it
- Generator input is a latent variable $z$ with a simple prior (e.g., uniform random or standard normal)
- Generator output is an image
- Generator network learns a function to map $P(z)$ to a distribution that approximates $P(x)$

## Generator Architecture Example

![[generator_architecture_example.png]]

## Discriminator
- Discriminator learns to identify real inputs vs. fake inputs created by generator 
- Neural network classifier with two output classes (real, fake)
- Architecture depends on task: e.g., for images the discriminator might be a CNN with several convolutional layers, followed by softmax

## Training
- The networks are trained together on a combination of real data $x$ and generator input $z$
- Given a generator $G$ and discriminator $D$:
	- Discriminator's goal is to correctly classify real vs. fake
	- Discriminator wants to maximise $D(x)$ and minimise $D(G(z))$
	- Generator's goal is to fool the Discriminator
	- Generator wants to maximise $D(G(z))$
- Can treat as a zero-sum game with the goal of finding equilibrium between $G$ and $D$

## Training Objective
- GAN training objective is a minimax game:

![[training_objective.png]]

## Training

![[training.png]]

- If the discriminator is too good:
	- Easily all fake inputs
	- Not much information to train the generator
- If the discriminator is too bad:
	- Easily confused by fake inputs that don't look real
	- Generator will learn a poor solution
- Training can be difficult - hard to find a balance between discriminator and generator

# Evaluating GANs

## GAN Evaluation
- GAN equilibrium does not necessarily mean the GAN has found a good solution
- How to tell if a GAN has learned? Ideally:
	- Outputs should not be identical to inputs (memorised training data)
	- Outputs should look like inputs (look "real" and not "fake")
	- Outputs should be diverse as real data (avoid **mode collapse** = the generator only creates one or a few outputs)

<iframe width="560" height="315" src="https://www.youtube.com/embed/G06dEcZ-QTg?si=xCKiNQVZ3XGRni-3" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
## Memorisation?

![[memorisation.png]]

## Realism?

![[realism.png]]

![[realism2.png]]

![[realism3.png]]

## Evaluating Realism
- How to evaluate realism?
- Gold standard: human evaluation (but is slow and expensive)
- Automatic methods compare response of an image classifier (e.g., a CNN trained on ImageNet) to real vs. GAN-generated image

## Inception Score

![[inception_score.png]]

- Within a class, all images should be confidently classified with the correct label
- Across classes, the GAN should produce a wide variety of confidently-classified images

- Advantages
	- Automatic, efficient
	- Neural network responses correlate with human judgements of image quality
- Disadvantages
	- Doesn't require high diversity within category
	- Sensitive to noise, adversarial images

## Diversity?
- The GAN isn't just memorising training examples
- But does it capture _all_ of the diversity in the training set?
	- How would you measure this?

## Birthday Paradox
- What are the odds that someone else in this subject has the same birthday you do?
	- About 59% $(=1 - (364/365)^{324})$
- What are the odds that any two people in this subject share a birthday?
	- Close to 100%
- What's the smallest class size that has at least a 50/50 chance of two people sharing a birthdauy?
	- 23

## Birthday Paradox for GANs
- Arora, Risteski, & Zhang (2018)
- Suppose a generator that can produce $N$ discrete outputs, all equally likely
- Experiment: take a small sample of $s$ outputs and count duplicates
	- The odds of observing duplicates in a sample of size $s$ can be used to compute $N$
	- A sample of about $\sqrt{N}$ outputs is likely to contain at least one pair of duplicates

## Duplicates and Diversity
- Example duplicates (and 1-NN in training dataset):

![[duplicates_and_diversity.png]]

- Most GANs tested produced about the same diversity (number of different images) as was in their training set

## Conditional GANs

## Conditional GANs
- Conditional model: learn $P(x|y)$ rather than $P(x)$
- Both discriminator and Generator take $y$ as additional input

![[conditional_gans1.png]]

- What if you don't have a dataset of real $x$-$y$ pairs?
- CycleGAN: train a pair of Generators to map $x \rightarrow y$ and $y \rightarrow x$

![[conditional_gans2.png]]

![[conditonal_gans4.png]]

## Example: CycleGAN

![[cyclegan.png]]