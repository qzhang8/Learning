This is my learning path for stable diffusion

To generate a picture based on some text prompts,  there are several problems to be answered

1. How to understand the semantics of the text, this can be resolved using a language model -  can be abstracted into a sequence to sequence problem.
2. How to generate a picture? A more basic problem is to generate a new picture based on a model that is learned from a large set of sample pictures, this is resolved using the Diffusion model.
3. Then the 3rd question is how to generate a piciture that is reflecting the semantics of the text, this is resolved using guided diffusion basically generate a picture "conditioned" on the text.

Diffusion model
To understand how Diffuion model work, we can first understand a concept called Varariontial Autoencoder, because the Diffusion model's mathematics's logic is very similar to Varational Autoencoder. Remarkably, Varational Autoencoder can be used to "understand" some sample pictures automatically, and then generate a new picture based this "understanding", this is I believe why this process is called "Autoencoder". And the diffusion model can also convert the sample pictures into another representation, and then generate new picitures based on the representation; both Diffusion & VAE utilizes the Bayesian probability model to handle the sample picutures, which is the reason that their arithmetic theory is similar.

So let's start from the Bayesion probability foundations.  Till now, the best text book giving me the best intuation is << A first class to the Machine Learning>>. 

1. Random variables: A variable that can be assigned to different values. And because of this multi-value assignment,  it can't be defined as a function. Please note I am using the word of "assign" instead of "mapping" here because mapping is usually used in the context of function.
2. Probability:  this is a function mapping the possibility of a Random variable assigned with a specific value. In the case of coin tossling, Random variable X is defined the result of one tossling. Obviously, X's value can be assigned to Head or Tail, so the tossling can't be described using a function, but the possibility of X= Head or X = Tail is defined (given one particular coin), the probability function is defined to map this possibility to a real number between [0, 1].  If the value of X can be assigned to any real number,  this probability function is also called Probability Density function.
3. There is a specicial category of Proabability Density function:  if the integral of the probility desentify function over all the mapping equals to 1,  these prabiblity Desenstion function is called Probability Distribution function (PDF). So the implication here is that not all Probablity Density function is integrated into 1. 

