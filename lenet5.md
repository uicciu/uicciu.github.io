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

## Workflow Pipeline

The model has already been trained using `create_CNN.py`. We provide the corresponding model (`simple_cnn_3+1_padding2.pth`) in the repository, which is ready for direct use.

### STEP1:第一层卷积层参数提取

```bash
python AttackCNN.py
```
完整且自动化提取以及恢复第一层卷积层权重，并进行误差分析，将重构好的权重和偏置分别保存为 layer1_recovered_weights.npy 和 layer1_recovered_bias.npy。

滤掉仅与第一层激活相关的点，筛选出能够渗透到第二层的关键候选点，并保存为 layer1_to_layer2_witnesses.pt。

### STEP2:第二层卷积层参数提取

#### 寻找并提取第二层关键点
```bash
python Attack_all.py
```
This script is largely consistent with the weight recovery workflow for the (2+1) CNN model. It serves as the master control program for the entire attack framework and implements a complete, fully automated chained attack pipeline for multi-layer convolutional neural networks.


---

