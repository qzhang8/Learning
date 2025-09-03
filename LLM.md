## the impact of quantization

4-bit quantization offers a trade-off between the LLMs’ capacity and the number of bits in the low-precision format. As the number of quantized bits decreases to 3 bits or lower, there is a noticeable performance discrepancy between the LLMs and their quantized counterparts. Experimental results suggest that when
the LLMs are quantized to 8 bits, the majority of LLMs, irrespective of their parameter scales, can maintain a performance level comparable to their
non-quantized equivalents. Moreover, LLMs that are quantized to 4 bits can also uphold similar performance to their non-quantized versions across
most benchmarks. However, if these LLMs are further quantized to 3 bits or lower, the capacity of these models begins to deteriorate. Notably, our
investigation reveals that when the LLMs are quantized to 2 bits using GPTQ, they lose their ability to comprehend and follow user instructions, resulting in the generation of incoherent text.

Perplexity is a reliable performance indicator for quantized LLMs on evaluation benchmarks

https://aclanthology.org/2024.findings-acl.726/

## Why DeepSeek is leading?

@robrita
7 days ago
0:00 – Open-source research and transparency (publishing model weights and technical details) can disrupt incumbent AI players.

1:08 – Differentiating between general-purpose models (V3) and specialized reasoning models (R1) is key for targeted improvements.

2:33 – Leveraging lower precision formats (8-bit and FP8 accumulation) enhances GPU efficiency and reduces training costs.

4:40 – Utilizing a mixture of experts architecture activates fewer parameters per token, significantly cutting computation.

5:21 – Employing multi-head latent attention (MLA) compresses key-value storage, reducing memory requirements and boosting throughput.

6:17 – Multi-token prediction (MTP) densifies training feedback, leading to faster, more coherent learning.

7:05 – Applying reinforcement learning specifically for complex, step-by-step reasoning enables models to self-correct and improve.

10:08 – Fine-tuning with structured reasoning examples prior to RL helps avoid language mixing and improves output clarity.

12:14 – Integrating optimization across GPU workloads and software tooling significantly lowers the cost of advanced AI systems.

https://www.youtube.com/watch?v=4Tmn-XP93m4


## Ultra-Wide Deep Nets and the Neural Tangent Kernel (NTK):
https://blog.ml.cmu.edu/2019/10/03/ultra-wide-deep-nets-and-the-neural-tangent-kernel-ntk/
