## A Beginner's Guide to Variational Methods: Mean-Field Approximation
https://blog.evjang.com/2016/08/variational-bayes.html
This blog provides the very basic introduction to the probability therory for varitional inference needed by understanding Stable Diffusion

##A Short Introduction to Entropy, Cross-Entropy and KL-Divergence
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
