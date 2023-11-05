# Convolutional Neural Networks II
## Learning Outcomes
- Implement max pooling and explain how/why downsampling is used in CNNs
- Implement regularisation methods and explain how/why they are used in CNNs
- Train a CNN and explain the design choices involved in the training process

# Downsampling in CNNs

## Convolutional Neural Network

![[convolutional_neural_network-againnn.png]]

## Downsampling in CNNs
- It's common to downsample convolutional layer output
- Reduces output size and number of computations needed in later layers
- Can also improve tolerance to translation - small changes in input won't change downsampled output

## Strided Convolution
- Convolutional strid = distance between successive convolution windows
- In CNN's, stride can be > 1
- Assuming no padding:
	- output_size = ceil((input_size - kernel_size + 1)/stride)
- With padding:
	- output_size = ceil(input_size/stride)

## Strided Convolution
- Advantage
	- Efficient - higher stride means fewer convolution operations
- Disadvantage
	- Kernel window skips over parts of the image, so important image features could be missed

## Convolutional Neural Network

![[ffs-another-cnn.png]]

## Max Pooling
- After convolution, each activation map is separately downsampled
- Max pool stride determines the amount of downsampled (output_size = input_size / stride)

![[max_pooling.png]]

- Within a given window in the activation map, take the highest value and discard the rest

![[max_pooling2.png]]

## Average Pooling
- Within a given window in the activation map, average the values

![[average_pooling.png]]

## Max/Average Pooling
- Advantage
	- Max pooling is most likely to preserve the most important features, compared to strided convolution or average pooling
- Disadvantages
	- Average pooling "blurs" over features; important features may be lost
	- Pooling is slower than strided convolution

# Regularisation in CNNs

## Regularisation
- Due to the very high number of parameters, CNNs are prone to overfitting, even on large datasets
- Regularisation is usually needed to reduce overfitting
- Common options:
	- L1 or L2 regularisation]
	- Dropout
	- Early stopping

## L1, L2 Regularisation
- Adds an additional term to the loss function that encourages smaller values for the network parameters
- L1 regularisation adds the term: $\sum_i | \theta_i |$ 
	- Penalise the sum of the absolute value of all parameters
	- Encourages sparse representation - many parameters should be 0
- L2 regularisation adds the term: $\sum_i \theta_i ^ 2$
	- Penalises the sum of the squares of all parameters
	- Encourages small (but not 0) parameters
 
- Free parameters when adding regularisation:
	- How much weight to give regularisation term vs. other terms in the loss function
	- Which layers to include in regularisation - all layers or just later layers?
	- Which parameters to include - sometimes only weights are included, not biases
- Adding regularisation tends to slow down training
- Too much regularisation can result in underfitting

## Convolutional Neural Network

![[ffs-yet-another-cnn.png]]

## Dropout
- Randomly discard some neurons (set output = 0)
- Forces to find useful features independently of each other
- Effectively, trains multiple architectures in parallel

![[dropour.png]]

- What percentage of neurons to drop is a free parameters (e.g., drop 50% or drop 20%)
- Can be applied to all layers, or just later layers
	- Different dropout percentages can be applied to different layers - typically later layers would have more dropout
- Adding dropout tends to slow down training
- Dropout is _only_ used in training - when evaluating the network on new data (validation/test), all neurons are active


## Early Stopping
- Stop training the network when it shows signs of overfitting
- Monitor performance on **validation** set
	- Subset of data not seen in training and not included in test set
	- During training, periodically check model's performance on the validation set - a decrease suggests overfitting
- Encourages smaller values for network parameters by keeping them close to their initial values (which are typically near 0)

# Training an Image Recognition CNN

## CNN Overview
- Typical architecture for image recognition:
	- Some number of convolutional layers, with downsampling
	- One or more fully-connected layers
	- Softmax output with cross-entropy loss
- Basic idea:
	- Do **feature embedding** in convolutional layers (transform images from pixels to useful high-level features)
	- Fully-connected layers are effectively a linear classifier (or MLP) to predict class from high-level features

## Convolutional Neural Network

![[ffs-yet-another-goddamn-cnn.png]]

## Loss Function: Softmax
- Apply softmax function to last layer's output:

$$
\sigma(y_i) = \frac{e^{y_i}}{\sum^N_{j=1}e^{y_i}}
$$

- Produces a vector that has the properties of a probability distribution:
	- All values in range 0-1
	- Values sum to 1

![[loss_function_softmax.png]]

## Loss Function: Cross-Entropy Loss
- Measure of the difference between the model and ground truth probability distributions

![[loss_function_crossentropy.png]]

- Cross-entropy loss betwen predicted class and ground-truth class:

![[cross-entropy-formula.png]]

## Training Process
- Split data into training/validation/test sets
- Split training data into batches
- For $N = 1 - ?$
	- Preprocess a batch of image data
	- Classify batch, compute loss
	- Update model parameters with backprop
- Periodically check trained model's performance on the validation set (for early stopping)

## Data Preprocessing
- Image whitening - scale each image to 0-255 range, then normalise so each pixel has mean=0 and (optionally) std=1

![[data_preprocessing.png]]

## Data Augmentation
- Manipulate training data to generate more samples
- Without data augmentation, even smaller networks (e.g., AlexNet) overfit to ImageNet

![[data_augmentation.png]]

- Common options:
	- Random crops (e.g., 224x224 from 256x256 images)
	 - Horizontal reflection
	 - Small colour/contrast adjustments (to simulate different camera settings or times of day)
- Less common:
	- Random rotation (e.g., +/- 15 degrees) $\leftarrow$ slow
	- Random scale $\leftarrow$ slow
	- Random occluders

- Why not include other variations?
	- Vertical reflection
	- Large colour changes

![[flipping-the-bird.png]]

## Training Process
- Initialise network weights and bias
	- Typically, weights initialised to small values from a Gaussian distribution around zero
	- Bias initialised to zero or small positive values
- Set training parameters
	- Batch size
	- Optimiser
	- Learning rate + decay
- Monitor training and validation loss
	- Stop training when validation loss no longer decreases

## Batch Size
- Batch size (or mini-batch size) = portion of the training data used to compute gradient for parameter update
- It's not computationally feasible to use the whole dataset to compute each update
- Dataset is randomly split into $N$ batches of size $b4
- $N$ updates = 1 epoch (every image has been seen once)

- Smaller batch size
	- More updates (but these are faster to compute)
	- Noisier updates (high variance in gradient)
- Larger batch size
	- Fewer updates (but each update takes longer to compute)
	- More stable updates
- In practice, batch size tends to be limited by memory constraints

## Optimiser
- Stochastic Gradient Descent (SGD)
- Root Mean Square Propagation (RMSProp)
- Adaptive Moment Estimation (Adam)
	- Keep a moving average of the squared gradient/gradient to divide the learning rate
	- Different from SGD that maintains a single learning rate for different gradients with different magnitudes

## Learning Rate + Decay
- Learning rate = how much to change network parameters on each update
	- Too high rate - unstable training
	- Too low rate - very slow learning

![[learning_rate_optimiser.png]]

## How Long to Train?
- Generally, train until model's performance on a validation set stops improving

![[how-long-to-train.png]]

# CNN Results

## Classification Performance

![[classification-performance.png]]

![[cnn-results2.png]]

![[classification-performance3.png]]

## Classification Errors

![[classification-errors.png]]

- Performance of top models on ImageNet vs. ImageNetV2
- Dropout of about 10% suggests some overfitting to quirks of ImageNet

![[classification-errors2.png]]