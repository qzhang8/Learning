This is my learning path for stable diffusion

To generate a picture based on some text prompts,  there are several problems to be answered

1. How to understand the semantics of the text, this can be resolved using a language model -  can be abstracted into a sequence to sequence problem.
2. How to generate a picture? A more basic problem is to generate a new picture based on a model that is learned from a large set of sample pictures, this is resolved using the Diffusion model.

Diffusion model
To understand how Diffuion model work, we should first understand a concept called Varariontial Autoencoder. Remarkably, this technology can be used to "understand" some sample pictures automatically, and then generate a new picture based this "understanding", this is I believe why this process is called "Autoencoder".

