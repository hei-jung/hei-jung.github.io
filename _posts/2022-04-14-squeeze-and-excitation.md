---
title: "[논문리뷰] Squeeze and Excitation Network"
categories: paper-review
tags: machine-learning python pytorch
toc: true
toc_sticky: true
---

# SENet 논문 & 코드 리뷰

[논문: Squeeze-and-Excitation Networks](https://arxiv.org/abs/1709.01507)<br>
[Squeeze and Excitation Networks Explained with PyTorch Implementation](https://amaarora.github.io/2020/07/24/SeNet.html),

## My Preview

내 연구 주제와 비슷한 주제의 논문을 찾던 중, 데이터도 비슷한 걸 사용한 논문을 발견했는데 거기서 Squeeze and Excitation Residual Network (SE-ResNet)을 사용하고 있었다.
마침 최근에 같은 랩실 친구가 SE-Net을 써봤다는 얘기를 들은 적이 있어서 나도 이 논문을 공부해봐야겠다는 생각이 들었다.

## 0. Abstract

The central building block of convolutional neural networks (CNNs) is the convolution operator, which enables networks to construct informative features by fusing both spatial and channel-wise information within local receptive fields at each layer.

CNN (convolutional neural network)의 중심 블럭은 컨볼루션 연산기이고 얘는 네트워크가 각 레이어를 따라 유의미한 정보를 담고 있는 feature를 구성하도록 만든다.

A broad range of prior research has investigated the spatial component of this relationship, seeking to strengthen the representational power of a CNN by enhancing the quality of spatial encodings throughout its feature hierarchy.

대부분의 선행 연구들은 이러한 관계의 spatial component를 조사했다. feature hierarchy를 따라서 spatial encoding의 품질을 높이는 방식으로 CNN의 representation 능력을 향상하는 것을 목표로 하였다.

In this work, we focus instead on the channel relationship and propose a novel architectural unit, which we term the “Squeeze-and-Excitation” (SE) block, that adaptively recalibrates channel-wise feature responses by explicitly modelling interdependencies between channels.

이 연구에서는 채널 간 관계나 "Squeeze-and-Excitation" (SE) 블럭이라고 하는 새로운 구조 단위를 제시하기보다도, 걔가 채널 간 상호의존성을 명시적으로 모델링하여 channel-wise feature를 적절히 재보정하는 것에 집중한다.

We show that these blocks can be stacked together to form SENet architectures that generalise extremely effectively across different datasets.

이 블럭들을 쌓아서 다양한 데이터셋에 대해 매우 효과적으로 generalize할 수 있음을 보인다.

We further demonstrate that SE blocks bring significant improvements in performance for existing state-of-the-art CNNs at slight additional computational cost.

SE block이 최신 CNN 모델 구조의 성능을 현저히 향상시키면서도 추가 연산 시간이 많이 늘어나지 않음을 보인다.

Squeeze-and-Excitation Networks formed the foundation of our ILSVRC 2017 classification submission which won first place and reduced the top-5 error to 2.251%, surpassing the winning entry of 2016 by a relative improvement of ∼25%. Models and code are available at https://github.com/hujie-frank/SENet.
