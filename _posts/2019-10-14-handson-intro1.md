---
layout: post
title: "[핸즈온 머신러닝] ML 종류들"
date: 2019-10-14 17:00:00
author: Dojin Kim
categories: ML
tags: cv ml handson 
cover:  "/assets/imgs/yolo_cover.jpg"
---

#### [Hands On ML](https://www.academia.edu/37010160/Hands-On_Machine_Learning_with_Scikit-Learn_and_TensorFlow)
위 PDF를 공부하며 내용을 정리하고 있습니다. 이미지들도 해당 PDF에서 가지고 왔습니다.

# Chapter 1 - Part 1

# ML system의 종류

## Supervised Learning (지도학습)

Supervised Learning은 **data와 label(레이블)들**의 페어들을 컴퓨터에게 줘서 학습시키는 방법이다. 사람이 직접 문제와 답을 컴퓨터에게 알려주고 컴퓨터는 수많은 data-label 페어들을 학습하는 것이다. 

예를 들어, 수 만장의 고양이 사진들과 고양이라는 명시적인 label을 컴퓨터한테 학습을 시키면, 컴퓨터는 수 만장의 사진들을 보면서 규칙이나 패턴을 파악하면서 학습을 한다. 컴퓨터는 어떠한 패턴을 갖는 사진이면 고양이가 있다고 판단하게 되는 것이다.

전형적인 Supervised Learning에는 2가지 종류가 있다:

1. Classification(분류)
2. Regression(회귀)

Classification(분류) 문제는 위의 예시처럼 데이터가 주어졌을 때 특정 값 혹은 label로 분류하는 문제를 의미한다. 주어진 사진이 고양이다 혹은 고양이가 아니다 라는 결과가 나오기 때문에 **discrete(이산적, 비연속적)**한 성질을 가지고 있다.

Regression(회귀) 문제는 주로 **continuous(연속적)** 성질이 있는 데이터들을 다룰 때 사용된다. 예를 들어, 2018년도 1~12월에 한국에 방문한 외국인과 방문한 장소들을 학습하고 나서 2019년 1월에는 몇명 정도가 경복궁을 방문할 것인지에 대한 것은 regression 문제이다. 

핸즈온 머신러닝 책에서는 다음의 supervised learning 알고리즘들을 다룰 예정이다:

- k-Nearest Neighbors
- Linear Regression
- Logistic Regression
- Support Vector Machines (SVMs)
- Decision Trees and Random Forests
- Neural networks

## Unsupervised Learning (비지도 학습)

Unsupervised Learning은 **data는 주어지지만 label을 주지 않고** 컴퓨터를 학습시키는 방법이다. ****컴퓨터가 스스로 주어진 데이터들로 패턴이나 feature(특징)들을 찾아내게 하는 학습 방법이다. 

<div align="center">
<img src="/assets/imgs/ml/intro1_1.png" style="width:500px"/>
</div>

Unsupervised learning 알고리즘들의 종류는 다음과 같이 있다:

- Clustering
    - K-Means
    - DBSCAN
    - Hierarchical Cluster Analysis (HCA)
- Anomaly detection and novelty detection
    - One-class SVM
    - Isolation Forest
- Visualization and dimensionality reduction
    - Principal Component Analysis (PCA)
    - Kernel PCA
    - Locally-Linear Embedding (LLE)
    - t-distributed Stochastic Neighbor Embedding (t-SNE)
- Association rule learning
    - Apriori
    - Eclat

알고리즘들을 간략하게 살펴보면, 

- clustering으로 비슷한 방문자들의 그룹을 detect하는데 사용될 수 있다. 
- hierarchical clustering으로 그룹을 더 sub-그룹들로 나눌 수 있다.
- anomaly detection은 unusual한 작업들을 잡아낼 수 있다.
- novelty detection도 비슷하나, 이 detection의 training set은 노멀해야 한다.
- visualization으로 복잡한 unlabeled 데이터로 2D 3D로 나타낼 수 있도록 한다 (가능한 구조를 유지하려고 한다).
- dimensionality reduction으로 정보를 최소한 손실시키지 않으면서 데이터를 간소화하려고 한다.
- associate rule learning은 많은 데이터들로 attribute간 관심있을 만한 relation을 발견하려고 한다. ex) 바베큐 소스와 갑자칩 사는 사람은 스테이크를 사려는 경향이 있다.

## Reinforcement Learning (강화학습)

`Agent`라는 learning system이 context에서 환경을 관찰하고 어떤 행동을 할 지 선택하고 행동한다. 그리고 그것에 대해 reward 혹은 penalty를 받는다. 컴퓨터는 스스로 `policy`라고 불리는 best 전략을 배운다. `Policy`는 `agent`가 주어진 상황에서 어떤 행동을 취해야 하는지 정의한다.  

<div align="center">
<img src="/assets/imgs/ml/intro1_2.png" style="width:500px"/>
</div>


지금까지는 ML 학습 방법의 종류들을 크게 알아보았고 Chapter1-Part2 에서는 Batch & Online 학습 / Instance & Model 기반 학습 / Testing과 Validating에 대해 알아볼 예정이다.