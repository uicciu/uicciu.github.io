# Algebraic Attack on Convolutional Neural1 Network with Max Pooling

## Project Overview

This repository provides tools to recover convolutional layer parameters (weights, biases) from 经典LeNet5模型

## Model Architecture

The model follows a LeNet-5–style architecture with two convolution–pooling blocks followed by three fully connected layers for 10-class classification.

1 × 32 × 32

→ Conv(1→6, 5×5) + ReLU → MaxPool(2×2) → 6 × 14 × 14

→ Conv(6→16, 5×5) + ReLU → MaxPool(2×2) → 16 × 5 × 5

→ Flatten (400)

→ FC1(400→120) + ReLU

→ FC2(120→84) + ReLU

→ FC(84→10)



## Explanation of this Codebase
This codebase bases its structure from Carlini et al's codebase that can be found under https://github.com/google-research/cryptanalytic-model-extraction.All convolutional and max-pooling specific components are implemented independently in this work.


## Installation
```bash
pip install torch numpy scipy jax jaxlib matplotlib networkx
```


