<div align="center">

# ParrotAI-1.1B
English

</div>

ParrotAI is a compact **1.1B-parameter** Llama-family language model designed to run efficiently on constrained hardware. It follows the **Llama 2-style architecture and tokenizer**, making it compatible with many open-source projects and toolchains across the Llama ecosystem.

ParrotAI was forked to serve **Synapta OS** as a **base model** for an education-first operating system that enables **rural schools** to use **artificial intelligence without internet access**. The project is released under the **Apache 2.0** license, consistent with the original TinyLlama project.

ParrotAI is built to maintain a small memory footprint while remaining useful across common NLP and assistant-style tasks. Its size makes it a practical choice for deployments where compute and RAM are limited.

## Potential Usecase
Small but capable language models are useful for many applications. Here are some potential usecases:
- Assisting speculative decoding of larger models.
- Deployment on edge devices with restricted memory and computational capacities, including scenarios where **no internet connection** is available (e.g., 4-bit quantized weights in the hundreds of MB range).
- Enabling real-time dialogue generation in applications such as games or local assistants.

Moreover, this code can be a **reference for enthusiasts keen on pretraining language models under 5 billion parameters** without diving too early into heavy distributed training stacks.

## Model & Training Details

| Setting                         | Description                                                    |
|---------------------------------|----------------------------------------------------------------|
| Parameters                      | 1.1B                                                           |
| Attention Variant               | Grouped Query Attention                                        |
| Model Size                      | Layers: 22, Heads: 32, Query Groups: 4, Embedding Size: 2048, Intermediate Size (Swiglu): 5632 |
| Sequence Length                 | 2048                                                           |
| Batch Size                      | 2 million tokens (2048 * 1024)                                 |
| Learning Rate                   | 4e-4                                                           |
| Learning Rate Schedule          | Cosine with 2000 warmup steps                                  |
| Training Data                   | Slimpajama & Starcoderdata                                     |
| Data Preprocessing              | Excluded GitHub subset of Slimpajama; sampled code from Starcoderdata |
| Combined Dataset Size           | Around 950B tokens                                             |
| Total Tokens During Training    | 3 trillion                                                     |
| Natural Language to Code Ratio  | 7:3                                                            |
| Hardware                        | 16 A100-40G GPUs                                               |

## Performance and Efficiency

### Training throughput (reference)
With common performance optimizations (see below), the codebase can reach throughput on the order of **~24k tokens/sec per A100-40G GPU**, translating to strong utilization without activation checkpointing (and typically higher utilization on larger-memory A100 variants).

### Inference throughput (reference)
Below are example throughputs commonly observed for this model class:

| Framework | Device | Settings | Throughput (tokens/sec) |
|-----------|--------|----------|--------------------------|
| Llama.cpp | Mac M2 16GB RAM | batch_size=1; 4-bit inference | ~71.8 |
| vLLM      | A40 GPU | batch_size=100, n=10 | ~7094.5 |

## Blazingly Fast
This codebase supports high-performance features such as:
- multi-gpu and multi-node distributed training with FSDP
- flash attention 2
- fused layernorm
- fused swiglu
- fused cross entropy loss
- fused rotary positional embedding

Credit: flash attention 2, fused layernorm, fused cross entropy loss, and fused
rotary positional embedding are from the FlashAttention repo. Fused swiglu is from xformers.

## Pretrain
Please refer to [PRETRAIN.md](PRETRAIN.md) for instructions on how to pretrain ParrotAI.

## Finetune
We include a simple full-parameter finetuning & inference script in [sft](sft).  
For finetuning with less than 4GB RAM, consider QLoRA and bitsandbytes.

## Acknowledgements
This repository is built upon [lit-gpt](https://github.com/Lightning-AI/lit-gpt) and [flash-attention](https://github.com/Dao-AILab/flash-attention).

## Fork Notice
This repository is a fork of **TinyLlama**. All original credit belongs to the TinyLlama authors and contributors, and usage remains subject to the original licenses and attributions.
