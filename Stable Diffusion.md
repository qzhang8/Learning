## This is my learning path for stable diffusion

To generate a picture based on some text prompts,  there are several problems to be answered

1. How to understand the semantics of the text, this can be resolved using a language model -  can be abstracted into a sequence to sequence problem.
2. How to generate a picture? A more basic problem is to generate a new picture based on a model that is learned from a large set of sample pictures, this is resolved using the Diffusion model.
3. Then the 3rd question is how to generate a piciture that is reflecting the semantics of the text, this is resolved using guided diffusion basically generate a picture "conditioned" on the text.

## Diffusion model
To understand how Diffuion model work, we can first understand a concept called Varariontial Autoencoder, because the Diffusion model's mathematics's logic is very similar to Varational Autoencoder. Remarkably, Varational Autoencoder can be used to "understand" some sample pictures automatically, and then generate a new picture based this "understanding", this is I believe why this process is called "Autoencoder". And the diffusion model can also convert the sample pictures into another representation, and then generate new picitures based on the representation; both Diffusion & VAE utilizes the Bayesian probability model to handle the sample picutures, which is the reason that their arithmetic theory is similar.

So let's start from the Bayesion probability foundations.  Till now, the best text book giving me the best intuation is << A first class to the Machine Learning>>. 

1. Random variables: A variable that can be assigned to different values. And because of this multi-value assignment,  it can't be defined as a function. Please note I am using the word of "assign" instead of "mapping" here because mapping is usually used in the context of function.
2. Probability:  this is a function mapping the possibility of a Random variable assigned with a specific value. In the case of coin tossling, Random variable X is defined the result of one tossling. Obviously, X's value can be assigned to Head or Tail, so the tossling can't be described using a function, but the possibility of X= Head or X = Tail is defined (given one particular coin), the probability function is defined to map this possibility to a real number between [0, 1].  If the value of X can be assigned to any real number,  this probability function is also called Probability Density function.
3. There is a specicial category of Proabability Density function:  if the integral of the probility desentify function over all the mapping equals to 1,  these prabiblity Desenstion function is called Probability Distribution function (PDF). So the implication here is that not all Probablity Density function is integrated into 1. 

The Bayesion probability is to focus on the study of Joint Probability and Conditional Probabibility. Both of them invovle in two or more Random Variables. Joint Probability function maps the possibility of multiple Random Variables assignment results. An example is the possiblity of two person's heights. In normal case, if these two persons are not direct-relatives, their height should be independent to each other.  But if one person is another person's parent or child,  the height of these two persons should have some impact to each other.  Say, if B is the child of A, then probability(height (B)) is conditioned on probability(height (A)), denoated as P(height(B)|height(A)) or short handed into P(B|A).   In general, the Bayesion rule defines the relationship between the probability of two Random Variables:
          P(B|A) = P(A|B)*P(B)/P(A)
 This equation is the result of an easy reasoning:  P(A, B) = P(A)*P(B|A) = P(B)*P(A|B) but all the magics start from this very simple equation.

 Let's assume Random Variables A and B are dependent to each other, but B is the Random Variable (data) that we can observe/measure while A is a hidden Random Variable that is unknown to us. A typical scenario to this B is the Coin Tossiling Variable while A denotes the Random Variable that the Coin is two sides equally weighted. In the context of VAE or Stable Diffusion,  one can treat each pixel in the picture as a Random Variable and then the whole image is a collection of Random Variables following the same Probability function. In this case, the Random Variables can be represented as a vector of scalar Random Variables.

Similar to other basic and important rules,  each term in this Bayesion equation has a deciated name:
.  P(B) is called Prior Probablity of B, this is the assumped Probability function of B without any other condition
.  P(B|A) is called Postier Probability of B,  this is the refined Probability function of B based on the observed Randam Variable A
.  P(A|B) is called the Likelihood of Random Variable conditioned on Random Variable B
.  P(A) is called Marginal Likelihood; in the discrete scenario,  this is also called "Total probability of A". 
 
There is a good example to understand this Bayesion rule. Let's assume for the biological reason, color blind rate among males is higher than color blind rate among females; let's assume male color blind probablity is 7% while female color bind probability is 1%; while for some reason, male population is lower than female in this country, e.g, male is 40% but female is 60%.  Now you hear someone is color blinded,  please infer the probablity of this person's gender.

Based on all these conditions, we can get the following probabilities:
P(C|M) = 7%;  P(~C|M) = 93%; P(C|F) = 1%; P(~C|F) = 99%; 

We can calculate P(M|C) = P(C|M)*P(M)/P(C); we have P(C|M) and P(M), and in this case we can calculate P(C) = 40%*7%+60%*1% = %3.4;  so P(M|C) = 7%*40%/%3.4 = 82.4%




 

 
          

