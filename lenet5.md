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

The model has already been trained using `create_CNN.py`. We provide the corresponding model (`simple_cnn_e.pth`) in the repository, which is ready for direct use.

### STEP1:第一层卷积层参数提取

```bash
python AttackCNN.py
```
完整且自动化提取以及恢复第一层卷积层权重，并进行误差分析，将重构好的权重和偏置分别保存为 layer1_recovered_weights.npy 和 layer1_recovered_bias.npy。

滤掉仅与第一层激活相关的点，筛选出能够渗透到第二层的关键候选点，并保存为 layer1_to_layer2_witnesses.pt。

### STEP2:第二层卷积层参数提取

#### 寻找并提取第二层关键点
```bash
python AttackLayer2_findwitnesses.py
```
使用第一层的恢复参数,筛选出能够穿透第一层、直接反映第二层神经元行为的Valid Witnesses,将验证通过的点分批次(BATCH_SIZE)存储在 results/BlackBox_witnesses/ 目录下。

#### 将第二层关键点进行聚类
```bash
python AttackLayer2_gather.py
```
将第二层关键点进行聚类，分别存储对应法向量(存储在results/BlackBox_layer2_results/ 目录下)，以及为后续第三层（Layer 3）攻击筛选有效关键点，存储于layer2_to_layer3_witnesses.pt。

#### 恢复第二层权重
```bash
python AttackLayer2.py
```
在已知第一层参数的基础上，通过聚合前期采集的关键点数据，利用数学推导解算出第二层的权重和偏置。

#### 误差分析
```bash
python check_solution.py
---
在黑盒攻击场景下，验证通过观察输入输出扰动提取到的参数，在数学上是否与目标模型等价。

---

