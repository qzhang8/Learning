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

## A Beginner's Guide to Variational Methods: Mean-Field Approximation
https://blog.evjang.com/2016/08/variational-bayes.html
This blog provides the very basic introduction to the probability therory for varitional inference needed by understanding Stable Diffusion

## A Short Introduction to Entropy, Cross-Entropy and KL-Divergence
https://www.youtube.com/watch?v=ErfnhcEV1O8


## Attention
https://web.stanford.edu/class/cs224n/slides/cs224n-2023-lecture08-transformers.pdf
We can think of attention as performing fuzzy lookup in a key-value store.
In a lookup table, we have a table of keys that map to values. The query matches one of the keys, returning its value.
In attention, the query matches all keys softly, to a weight between 0 and 1. The keys’ values are multiplied by the weights and summed.

## reparameterization trick
An excellent illustration here. https://stats.stackexchange.com/questions/199605/how-does-the-reparameterization-trick-for-vaes-work-and-why-is-it-important

## Why does modeling a conditional probability mean learning to predict the conditional score function?
When modeling a conditional probability, you're essentially trying to capture the relationship between two events and predict the likelihood of one event (Y) occurring given the other event (X) has already happened. This likelihood is represented by the conditional probability P(Y | X).

However, directly predicting the probability itself might not always be the most efficient or effective approach. Instead, we often rely on a score function, also known as a log-likelihood function. Here's why:

Challenges of directly predicting probability:

Non-linearity: Probability values lie between 0 and 1, and directly modeling this range with neural networks can be challenging due to their non-linear activation functions.
Numerical instability: Working with extremely small or large probability values during calculations can lead to numerical instability and inaccurate results.
Benefits of using a score function:

Linearity: Score functions, typically the log-likelihood, are often linearly related to the probability. This makes them easier to model using the linear activation functions common in neural networks.
Numerical stability: Logarithms help compress the range of probabilities (0-1) to a more manageable range (negative infinity to positive infinity), mitigating numerical stability issues.
What is a conditional score function?

In the context of conditional probabilities, the score function becomes the conditional score function. This function, denoted as S(Y | X), aims to capture the relative ordering, or ranking, of different possible values of Y given X.

Interpretation of the score function:

A higher score S(y | x) for a specific value of Y implies a higher likelihood of that Y occurring given the observed X, compared to other possible values of Y.
Conversely, a lower score indicates a lower likelihood of that Y happening.
Learning the score function:

Many machine learning algorithms, when tasked with modeling conditional probabilities, are ultimately trained to predict the conditional score function. By learning this function, the model indirectly captures the underlying relationship between X and Y, allowing it to predict the relative likelihood of different Y values given a specific X.

Summary:

While the ultimate goal is to understand and predict the conditional probability P(Y | X), modeling the conditional score function S(Y | X) often proves to be a more efficient and robust approach due to its linear relationship with the probability and improved numerical stability. The score function indirectly encodes the information required to rank and compare different Y values based on their likelihood given X.


### ELBO maximization ###

### Reparametrization trick ###
