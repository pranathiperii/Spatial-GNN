# Spatial Transcriptomics and GNN

This time I wanted to see what's spatial transcriptomics is all about. I dont know about you but I hear about it very often. After gathering the basics and the concepts, I wanted to build a model (ambitious i know) that takes H&E images as inputs and predicts gene expression as an output. This was through using Graph Neural Network trained on 10x Visium spatial transcriptomics data from a mouse brain section.

---
## Motivation

H&E staining is cheap, fast, and done routinely in every hospital. There are many attempt to use these images to predict gene expression. By predicting gene expression from morphology alone, we can work with accessible data at a much cheaper cost to study the spatial patterns of diseases. This in other words, states strong potential for diagnostics translational clinical research. 

---
## Method

I used a publicly available mouse brain Visium dataset from squidpy. The AnnData object contains 2688 tissue spots, 18078 genes, spatial coordinates for every spot, and the original H&E tissue image.
From 18078 genes I selected the top 200 highly variable genes, those that differ most in expression across spots. These are the most biologically informative and the most learnable. The result is Y: a 2688 × 200 matrix where each row is a spot and each column is a gene's expression value.
For each spot, we crop a 128×128 pixel patch from the H&E image centered on that spot's coordinates. Each patch is passed through a pretrained ResNet50 (ImageNet weights, classification head removed) to produce a 2048-dimensional feature vector capturing tissue morphology cell density, nuclear arrangement, staining texture. The result is X: a 2688 × 2048 matrix. Features are normalized with StandardScaler.

---
## Result

In technical terms, Mean Pearson R: 0.50 across 200 highly variable genes
109/200 genes predicted at R > 0.5. Mean R~0.5 means on average the model captures about half the spatial variance in gene expression from morphology alone. 109 out of 200 genes predicted with R>0.5. For genes well-predicted (R>0.7), the spatial pattern is so tightly linked to morphology that the model recovers it almost perfectly.

<img width="995" height="431" alt="image" src="https://github.com/user-attachments/assets/541ed3dd-e1b6-4dfa-8970-ee5252277eee" />
<img width="700" height="393" alt="image" src="https://github.com/user-attachments/assets/2ad7a7c3-0d0d-464a-82fd-542c282ce158" />

---
## Requirements
```
pip install torch_geometric scanpy squidpy anndata torchvision
```
---
The full notebook is in this repo. Run it on Google Colab with a T4 GPU.
