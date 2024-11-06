##https://arxiv.org/pdf/2208.11970
## This is my learning path for stable diffusion

To generate a picture based on some text prompts,  there are several problems to be answered

1. How to understand the semantics of the text, this can be resolved using a language model -  can be abstracted into a sequence to sequence problem.
2. How to generate a picture? A more basic problem is to generate a new picture based on a model that is learned from a large set of sample pictures, this is resolved using the Diffusion model.
3. Then the 3rd question is how to generate a piciture that is reflecting the semantics of the text, this is resolved using guided diffusion basically generate a picture "conditioned" on the text.

## Diffusion model
To understand how Diffuion model work, we can first understand a concept called Varariontial Autoencoder, because the Diffusion model's mathematics's logic is very similar to Varational Autoencoder. Remarkably, Varational Autoencoder can be used to "understand" some sample pictures automatically, and then generate a new picture based this "understanding", this is I believe why this process is called "Autoencoder". And the diffusion model can also convert the sample pictures into another representation, and then generate new picitures based on the representation; both Diffusion & VAE utilizes the Bayesian probability model to handle the sample picutures, which is the reason that their arithmetic theory is similar.

So let's start from the Bayesion probability foundations.  Till now, the best text book giving me the best intuation is << A first class to the Machine Learning>>. 

### Probability ###
1. Random variables: A variable that can be assigned to different values. And because of this multi-value assignment,  it can't be defined as a function. Please note I am using the word of "assign" instead of "mapping" here because mapping is usually used in the context of function.
2. Probability:  this is a function mapping the possibility of a Random variable assigned with a specific value. In the case of coin tossling, Random variable X is defined the result of one tossling. Obviously, X's value can be assigned to Head or Tail, so the tossling can't be described using a function, but the possibility of X= Head or X = Tail is defined (given one particular coin), the probability function is defined to map this possibility to a real number between [0, 1].  If the value of X can be assigned to any real number,  this probability function is also called Probability Density function.
3. There is a specicial category of Proabability Density function:  if the integral of the probility desentify function over all the mapping equals to 1,  these prabiblity Desenstion function is called Probability Distribution function (PDF). So the implication here is that not all Probablity Density function is integrated into 1. 

The Bayesion probability is to focus on the study of Joint Probability and Conditional Probabibility. Both of them invovle in two or more Random Variables. Joint Probability function maps the possibility of multiple Random Variables assignment results. An example is the possiblity of two person's heights. In normal case, if these two persons are not direct-relatives, their height should be independent to each other.  But if one person is another person's parent or child,  the height of these two persons should have some impact to each other.  Say, if B is the child of A, then probability(height (B)) is conditioned on probability(height (A)), denoated as P(height(B)|height(A)) or short handed into P(B|A).   In general, the Bayesion rule defines the relationship between the probability of two Random Variables:
          P(B|A) = P(A|B)*P(B)/P(A)
 This equation is the result of an easy reasoning:  P(A, B) = P(A)*P(B|A) = P(B)*P(A|B) but all the magics start from this very simple equation.

 Let's assume Random Variables A and B are dependent to each other, but B is the Random Variable (data) that we can observe/measure while A is a hidden Random Variable that is unknown to us. A typical scenario to this B is the Coin Tossiling Variable while A denotes the Random Variable that the Coin is two sides equally weighted. In the context of VAE or Stable Diffusion,  one can treat each pixel in the picture as a Random Variable and then the whole image is a collection of Random Variables following the same Probability distribution function. In this case, the Random Variables can be represented as a vector of scalar Random Variables.

Similar to other basic and important rules,  each term in this Bayesion equation has a deciated name:
  P(B) is called Prior Probablity of B, this is the assumped Probability function of B without any other condition
  P(B|A) is called Postier Probability of B,  this is the refined Probability function of B based on the observed Randam Variable A
  P(A|B) is called the Likelihood of Random Variable conditioned on Random Variable B, meaning how likely event A will happen with the current setting of      event B.
  P(A) is called Marginal Likelihood; in the discrete scenario,  this is also called "Total probability of A". 
 
There is a good example to understand this Bayesion rule. Let's assume for the biological reason, color blind rate among males is higher than color blind rate among females; let's assume male color blind probablity is 7% while female color bind probability is 1%; while for some reason, male population is lower than female in this country, e.g, male is 40% but female is 60%.  Now you hear someone is color blinded,  please infer the probablity of this person's gender.

Based on all these c onditions, we can get the following probabilities:
P(C|M) = 7%;  P(~C|M) = 93%; P(C|F) = 1%; P(~C|F) = 99%; 

We can calculate P(M|C) = P(C|M)*P(M)/P(C); we have P(C|M) and P(M), and in this case we can calculate P(C) = 40%*7%+60%*1% = %3.4;  so P(M|C) = 7%*40%/%3.4 = 82.4%.  In this case,  intuatively,  the possibility that one color blinded person is male should be 7X of that person is female (as P(C|M) = 7%; P(C/F) = 1%)), the possiblity of should be 7/8 = 87.5%, in reality the probabilty is lower because P(M) is only 40% of the total population.

### Expectation ###

https://borisburkov.net/2022-12-31-1/

### Monte Carlo methods ###
https://github.com/zsdonghao/deep-learning-book/blob/master/Split-pdf/Part%20III-%2017%20Monte%20Carlo%20Methods.pdf

### KL-Divergence ###

### ELBO maximization ###

### Reparametrization trick ###

## Auto Encoder ##
https://lilianweng.github.io/posts/2018-08-12-vae/#autoencoder

## VAE: Variational Autoencoder ##
https://lilianweng.github.io/posts/2018-08-12-vae/#vae-variational-autoencoder

## VAE: Loss function ##
https://lilianweng.github.io/posts/2018-08-12-vae/#loss-function-elbo

## VAE:  reparameterization trick ##
https://lilianweng.github.io/posts/2018-08-12-vae/#reparameterization-trick

## VAE CODE ##
Load and preprocess MNIST data

```python
(x_train, y_train), (x_test, y_test) = mnist.load_data()
x_train = x_train.astype('float32') / 255.
x_test = x_test.astype('float32') / 255.
x_train = x_train.reshape((len(x_train), np.prod(x_train.shape[1:])))
x_test = x_test.reshape((len(x_test), np.prod(x_test.shape[1:])))

Define VAE model
original_dim = 784  # Number of pixels in MNIST images
latent_dim = 2  # Dimensionality of the latent space

Encoder
inputs = Input(shape=(original_dim,))
h = Dense(256, activation='relu')(inputs)
z_mean = Dense(latent_dim)(h)
z_log_var = Dense(latent_dim)(h)

def sampling(args):
z_mean, z_log_var = args
epsilon = tf.random.normal(shape=(tf.shape(z_mean)[0], latent_dim))
return z_mean + tf.exp(z_log_var * 0.5) * epsilon

z = Lambda(sampling)([z_mean, z_log_var])

Decoder
decoder_inputs = Input(shape=(latent_dim,))
h_decoder = Dense(256, activation='relu')(decoder_inputs)
outputs = Dense(original_dim, activation='sigmoid')(h_decoder)

Instantiate VAE model
vae = Model(inputs, outputs)

Compile VAE model
def vae_loss(x, x_decoded_mean):
xent_loss = original_dim * tf.keras.losses.binary_crossentropy(x, x_decoded_mean)
kl_loss = - 0.5 * tf.reduce_mean(1 + z_log_var - tf.square(z_mean) - tf.exp(z_log_var))
return xent_loss + kl_loss

vae.compile(optimizer='adam', loss=vae_loss)

Train the VAE model
vae.fit(x_train, x_train, epochs=10, batch_size=128, validation_data=(x_test, x_test))
``
```python

Explanation:

Import necessary libraries: TensorFlow for building the model, Keras for the neural network API, NumPy for numerical operations, and Matplotlib for visualization.
Load and preprocess data: The MNIST dataset is loaded, normalized, and reshaped.
Define VAE architecture:
Encoder: Maps input images to latent space representations (mean and log variance of the latent distribution).
Sampling layer: Samples from the latent distribution using the reparameterization trick.
Decoder: Maps latent space representations back to reconstructed images.
Instantiate VAE model: Connects the encoder and decoder.
Compile VAE model: Defines the loss function (reconstruction loss + KL divergence regularization) and optimizer.
Train the VAE model: Fits the model to the training data.
Key points:

Reparameterization trick: This is used to make the gradients backpropagatable through the sampling process.
KL divergence: Encourages the latent distribution to be close to a standard normal distribution.
Reconstruction loss: Measures how well the decoder can reconstruct the original image from the latent representation.

In the context of the provided VAE code, the Dense function represents a fully connected layer in a neural network.

What does a Dense layer do?
Fully connected: Every neuron in a Dense layer is connected to every neuron in the previous layer.
Linear transformation: It performs a linear transformation on the input data, multiplying it by a weight matrix and adding a bias vector.
Activation function: Typically, an activation function is applied to introduce non-linearity. Common choices include ReLU, sigmoid, and tanh.

So, Dense(latent_dim)(h) essentially means:

Create a dense layer with latent_dim output neurons.
Apply this layer to the input h.
Return the output of the dense layer.
Context in VAE
In the context of the VAE, this expression is used to calculate the mean and log variance of the latent space distribution. h is the output of a previous layer, and latent_dim is the dimensionality of the latent space. By applying two dense layers to h, we obtain two vectors, z_mean and z_log_var, which represent the parameters of the latent distribution.

## from VAE to Hierarchical Variational Autoencoders to Variational Diffusion Models ##

https://arxiv.org/pdf/2208.11970

## from VDM to Score-based Generative Models ##

https://arxiv.org/pdf/2208.11970

## from Score-based Generative Models to Stable Diffusion ##




 




 

 
          

