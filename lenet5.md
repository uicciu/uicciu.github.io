# Algebraic Attack on Convolutional Neural1 Network with Max Pooling

## Project Overview

This repository provides tools to recover the parameters (weights and biases) of convolutional layers from the classic LeNet-5 model.

## Model Architecture

The model follows a **LeNet-5–style** architecture with two convolution–pooling blocks followed by three fully connected layers for 10-class classification.

1 × 32 × 32  
→ Conv(1→6, 5×5) + ReLU → MaxPool(2×2) → 6 × 14 × 14  
→ Conv(6→16, 5×5) + ReLU → MaxPool(2×2) → 16 × 5 × 5  
→ Flatten (400)  
→ FC1(400→120) + ReLU  
→ FC2(120→84) + ReLU  
→ FC(84→10)


## Workflow Pipeline

The model has already been trained using `create_CNN.py`. We provide the corresponding model (`simple_cnn_e.pth`) in the repository, which is ready for direct use.

### STEP1: Extraction of the First Convolutional Layer Parameters

```bash
python AttackCNN.py
```
This script fully and automatically extracts and recovers the weights of the first convolutional layer, performs error analysis, and saves the reconstructed weights and biases as `layer1_recovered_weights.npy` and layer1_recovered_bias.npy respectively.

It filters out points only relevant to the activation of the first layer, screens key candidate points that can propagate to the second layer, and saves them as `layer1_to_layer2_witnesses.pt`.

### STEP2:Extraction of the Second Convolutional Layer Parameters

#### Finding and Extracting Key Points for the Second Layer
```bash
python AttackLayer2_findwitnesses.py
```
Using the recovered parameters of the first layer, this script screens Valid Witnesses—points that penetrate the first layer and directly reflect the behavior of second-layer neurons. The validated points are stored in batches (BATCH_SIZE) in the `results/BlackBox_witnesses/ directory`.

#### Gathering Witnesses for the Second Layer
```bash
python AttackLayer2_gather.py
```
This script gathers and clusters the key points of the second layer, stores the corresponding normal vectors in the `results/BlackBox_layer2_results/ directory`, and screens valid key points for the subsequent attack on the third layer (Layer 3), which are saved as `layer2_to_layer3_witnesses.pt`.

#### Recovering Weights of the Second Layer
```bash
python AttackLayer2.py
```
Based on the known parameters of the first layer, this script calculates and solves the weights and biases of the second layer by aggregating the previously collected key point data and leveraging mathematical derivations.

#### Error Analysis
```bash
python check_solution.py
```
In a black-box attack scenario, this script verifies whether the parameters extracted by observing input-output perturbations are mathematically equivalent to those of the target model.

## Explanation of this Codebase
This codebase bases its structure from Carlini et al's codebase that can be found under https://github.com/google-research/cryptanalytic-model-extraction.All convolutional and max-pooling specific components are implemented independently in this work.


## Installation
```bash
pip install torch numpy scipy jax jaxlib matplotlib networkx
```

---

