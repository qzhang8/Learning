##https://arxiv.org/pdf/2208.11970
## This is my learning path for Deep Generative Model

To generate a picture based on some text prompts,  there are several problems to be answered

1. How to understand the semantics of the text, this can be resolved using a language model -  can be abstracted into a sequence to sequence problem.
2. How to generate a picture? A more basic problem is to generate a new picture based on a model that is learned from a large set of sample pictures, this is resolved using the Diffusion model.
3. Then the 3rd question is how to generate a piciture that is reflecting the semantics of the text, this is resolved using guided diffusion basically generate a picture "conditioned" on the text.

## Diffusion model
To understand how Diffuion model work, we can first understand a concept called Varariontial Autoencoder, because the Diffusion model's mathematics's logic is very similar to Varational Autoencoder. Remarkably, Varational Autoencoder can be used to "understand" some sample pictures automatically, and then generate a new picture based this "understanding", this is I believe why this process is called "Autoencoder". And the diffusion model can also convert the sample pictures into another representation, and then generate new picitures based on the representation; both Diffusion & VAE utilizes the Bayesian probability model to handle the sample picutures, which is the reason that their arithmetic theory is similar.



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




 




 

 
          

