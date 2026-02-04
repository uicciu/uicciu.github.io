# Algebraic Attack on Convolutional Neural1 Network with Max Pooling

## Overview

This repository provides tools to recover convolutional layer parameters (weights, biases) from CNN models, specifically designed for CIFAR-10 classification. 

## Model Architecture

We consider a lightweight CNN for CIFAR-10 consisting of two convolutional layers with max-pooling, followed by two fully connected layers:

3×32×32  
→ Conv(3→3, 3×3, pad=1) + ReLU → MaxPool(2×2)  
→ Conv(3→4, 3×3, pad=1) + ReLU → MaxPool(2×2)  
→ FC1(256→256) → FC2(256→120) → FC(120→10)

## Explanation of this Codebase
This codebase bases its structure from Carlini et al's codebase that can be found under https://github.com/google-research/cryptanalytic-model-extraction.All convolutional and max-pooling specific components are implemented independently in this work.


## Installation
```bash
pip install torch numpy scipy jax jaxlib matplotlib networkx
```

## EXTRACTING MODELS
Because the model has already been trained using `create_CNN.py` and saved in the `model/` directory, the parameters can be extracted directly.

extract it with
> python Attack.py 

check the quality of this extraction with
> python check_solution.py
