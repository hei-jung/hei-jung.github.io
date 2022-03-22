---
title: "[논문리뷰] Deep Imbalanced Regression"
categories: paper-review
tags: machine-learning python pytorch
toc: true
toc_sticky: true
---

# DIR 논문 리뷰

[논문: Delving into Deep Imbalanced Regression](http://proceedings.mlr.press/v139/yang21m.html), [Github](https://github.com/YyzHarry/imbalanced-regression)

## My Preview

### Imbalanced Regression

내가 학습에 사용하고 있는 데이터는 Medical data이다. Medical/Healthcare data는 다른 데이터에 비해서 특히 더 불균형(imbalanced)인 경우가 많다.
일반적으로 질병을 예측하기 위한 건강 정보는 병에 걸린 사람보다 걸리지 않은 건강한 사람의 비율이 훨씬 많기 때문이다.
(병변 데이터라고 하면 비교적 병변 수치가 작은 쪽으로 치우친 분포 형태인 right-skewed (positively skewed) distribution을 보이게 된다.)

1D 데이터 (주로 numerical data) 학습 시 imbalanced data handling에 대한 방법으로는
[SMOTE](https://www.jair.org/index.php/jair/article/view/10302)(Synthetic Minority Oversampling TEchnique),
[SMOGN](http://proceedings.mlr.press/v74/branco17a)(SMOte for regression with Gaussian Noise) 같은 것들이 알려져 있다.
그런데 내가 사용하고 있는 데이터의 경우 3D 이미지 데이터라서 SMOTE나 SMOGN 같은 방법을 적용하는 건 적절하지 않다.
내 데이터 뿐만 아니라 요즘은 영상/동영상 데이터 학습을 많이 하기 때문에 데이터 불균형을 손보기 위한 다른 방법들을 찾아봐야 한다.

**Delving into Deep Imbalanced Regression** 논문에서는 이런 나에게 필요한 imbalanced data handling 하는 방법을 소개하고 있다.

## 0. Abstract

기존의 데이터 불균형 문제를 해결하는 방식들은 범주형 데이터 (target with categorical indices)들의 불균형 문제에 초점을 맞추고 있다. (주로 classification task에서의 데이터 불균형)
본 논문에서는 연속적인 target value로 구성된 데이터로 학습할 때 데이터 불균형 문제 (Deep Imbalanced Regression; DIR)를 해결할 수 있는 방법을 제시한다.
범주형 label과 연속형 label의 차이점에서 착안하여 label과 feature에 대한 distribution smoothing 기법을 제안하고 있고 이는 imbalanced regression problem에서 활용할 수 있다.

## 1. Introduction

데이터 불균형 문제는 편재하다. 이 현상은 기계 학습에도 큰 방해요소가 되기 때문에 이미 전부터 이런 문제를 해결하는 방법들이 많이 제안되었다.

그러나 기존의 솔루션들은 일반적으로 class가 나뉘어 있는 이른바 classification 문제에만 주력하고 있다.
정작 현실 세계의 데이터는 연속적이고 범위가 무한한 값들로 이뤄져 있으며 이러한 연속값 데이터 안에서도 불균형 문제가 있는 경우가 많다.

DIR에서는 classification task에 대한 imbalanced data handling과는 다른 새로운 도전장을 내민다. (여기서 앞으로 타깃(target)이라고 하면 인공신경망 모델에 학습시킬 대상을 말한다.)<br>
먼저 연속적인 타깃 값들은 클래스 간 뚜렷한 경계가 없기 때문에 re-sampling이나 re-weighting 같은 전통적인 imbalanced classification 방식을 그대로 적용하기엔 애매해진다.<br>
그리고 연속적인 label들은 타깃 간에 뭔가 의미가 있을지도 모르는 거리를 가지는데 이건 우리가 data imbalance를 어떻게 해석해야 할지에 대한 힌트를 준다.
예를 들어서 training 데이터에서 t1, t2라는 두 개의 소수 클래스 타깃이 있다고 해보자. (정확히는 클래스는 아니지만 암튼)
그런데 t1은 인접값이 많이 포진되어 있는 영역 안에 있는 반면 ([t1-𝛥, t1+𝛥] 범위 안에 샘플 수가 많음), t2는 그렇지가 않다.
이런 경우에 t1은 t2와는 다른 정도의 불균형을 보이는 것이다.<br>
마지막으로 classification과 달리, 특정 타깃 값들은 데이터가 아예 존재하질 않아서 타깃의 extrapolation과 interpolation을 필요로 한다.

이 논문에서는 단순하면서도 효과적으로 DIR을 다루는 방법 두 가지를 선보인다: **label distribution smoothing (LDS)** and **feature distribution smoothing (FDS)**.
두 접근법에 대한 핵심적인 발상은 kernel distribution을 써서 인접한 타깃들 간의 유사성 (similarity between nearby targets)을 이용함으로써 label space와 feature space 상에서 distribution smoothing을 해주는 것이다.
두 기술 모두 기존 딥러닝 모델들에 쉽게 적용할 수 있고 end-to-end 방식의 optimization이 가능하다.
또한 이 기술들은 다른 방법들을 함께 사용했을 때 더욱더 효과적이다.

## 2. Related work

대충 기존의 **Imbalanced Classification** (SMOTE 등등...), **Imbalanced Regression** (SMOTE 활용...) 과는 차별화 되었다는 이야기.

## 3. Methods

<!--
### Problem settings

`{(xi, yi)}N,i=1`이라는 training set이 있다고 하자. (`xi`: 실수 input, `yi`: 실수 label)
여기서 label space `Y`와 타깃 값의 그룹 인덱스를 나타내는 `B` 집합을 사용할 것이다.
-->

### 3.1. Label Distribution Smoothing

### 3.2. Feature Distribution Smoothing

## 4. Benchmarking DIR

## 5. Conclusion
