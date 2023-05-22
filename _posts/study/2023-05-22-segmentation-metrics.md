---
title: "[머신러닝] image segmentation metrics"
categories: cv-and-ml
tags: python machine-learning pytorch
use_math: true
toc: true
toc_sticky: true
---

Image segmentation(이미지 분할)에서 자주 쓰이는 성능 평가 지표(metrics)들을 알아보자.

## Precision/Recall/F1-score

Precision, Recall, F1-score는 classification(분류) task 뿐만 아니라 segmentation task에서도 성능을 평가할 때 자주 쓰이는 애들이다.

편의를 위해 각각의 지표를 정의할 때 축약어를 사용하겠다.<br>

|    |예측|실제|
|----|---|---|
|TP(True positive)|+|+|
|FP(False positive)|+|-|
|TN(True negative)|-|+|
|FN(False negative)|-|-|

TP: 모델이 positive라고 예측한 값들 중에서 실제로도 positive인 값.<br>
FP: 모델이 positive라고 예측한 값들 중에서 실제로는 negative인 값.<br>
TN: 모델이 negative라고 예측한 값들 중에서 실제로는 positive인 값.<br>
FN: 모델이 negative라고 예측한 값들 중에서 실제로도 negative인 값.<br>

### Precision (정밀도) = PPV (Positive Predictive Value)

모델이 positive라고 답한 데이터 중에서 정답이 positive인 것들의 비율로, `0 ~ 1` 사이 값을 가지며 1에 가까울수록 좋다.

$ Precision = \frac{TP}{TP + FP} $

Precision을 높이려면 FP를 낮춰야 한다.

### Recall (재현율) = Sensitivity (민감도) = TPR (True Positive Rate)

정답이 positive인 데이터 중에서 모델이 positive라고 예측한 것들의 비율로, `0 ~ 1` 사이 값을 가지며 1에 가까울수록 좋다.

$ Recall = \frac{TP}{TP + FN} $

Recall을 높이려면 FN을 낮춰야 한다.

### F1-score

일반적으로는 precision과 recall을 combine한 형태인 F1-score를 많이 쓴다.<br>
F1-score는 precision과 recall의 조화 평균이고, `0 ~ 1` 사이 값을 가지며 1에 가까울수록 좋다.

$ F1-score = \frac{2 * Precision * Recall}{Precision + Recall} $

## Specificity (특이도) = TNR (True Negative Rate)

정답이 negative인 데이터 중에서 모델이 negative라고 예측한 것들의 비율로, `0 ~ 1` 사이 값을 가지며 1에 가까울수록 좋다.

$ Specificity = \frac{TN}{TN + FP} $

## Dice Similarity Coefficient (DSC), Dice Coefficient, Dice Score

DSC는 의료 영상 segmentation에서 성능 평가 지표로 흔히 쓰인다.<br>
예측된 map(Pred)과 정답 map(GT; ground-truth) 간 겹친 영역의 크기의 2배를, 두 map의 총 픽셀(pixel) 수로 나눈 값으로 정의할 수 있다.

$ DSC = \frac{2 \sum^N Pred GT}{\sum^N Pred^2 + \sum^N GT^2} $ (1)

DSC는 실제 값(GT)와 예측 값(Pred)이 얼마나 겹치는지 평가하는 IoU(intersection over union)와도 유사하다. (식 2-1)<br>
또한 DSC가 boolean data에 적용될 경우에는 분할을 수행할 영역(mask)을 positive class로 뒀을 때의 F1-score 식과 DSC 식이 본질적으로 같다. (식 2-2)

$$ DSC = \frac{2*|Pred \cap GT|}{|Pred| + |GT|} $$ (2-1)

$ DSC = \frac{2TP}{2TP + FP + FN} $ (2-2)

## Pixel Accuracy

Pixel Accuracy는 픽셀에 따른 정확도이다. pixel별로 정답 class를 맞췄는지의 여부(True/False)를 통해 계산한다.

$ Pixel Accuracy = \frac{TP + TN}{TP + TN + FP + FN} $

## 3D image segmentation metrics Pytorch 코드

```python
from torch import nn


class SegMetrics(nn.Module):
    def forward(self, inputs, targets, eps=0.0001):
        tp = (inputs * targets).sum(dim=4, keepdim=True).sum(dim=3, keepdim=True).sum(dim=2, keepdim=True)
        fp = (inputs * (1 - targets)).sum(dim=4, keepdim=True).sum(dim=3, keepdim=True).sum(dim=2, keepdim=True)
        fn = ((1 - inputs) * targets).sum(dim=4, keepdim=True).sum(dim=3, keepdim=True).sum(dim=2, keepdim=True)
        tn = ((1 - inputs) * (1 - targets)).sum(dim=4, keepdim=True).sum(dim=3, keepdim=True).sum(dim=2, keepdim=True)

        den1 = inputs.pow(index).sum(dim=4, keepdim=True).sum(dim=3, keepdim=True).sum(dim=2, keepdim=True)
        den2 = targets.pow(index).sum(dim=4, keepdim=True).sum(dim=3, keepdim=True).sum(dim=2, keepdim=True)

        pixel_acc = (tp + tn) / (tp + tn + fp + fn + eps)
        dice = (2 * tp / (den1 + den2 + eps))  # (1)
        dice = (2 * tp) / (2 * tp + fp + fn + eps)  # (2-2)
        precision = tp / (tp + fp + eps)
        recall = tp / (tp + fn + eps)
        specificity = tn / (tn + fp + eps)

        return pixel_acc.mean(), dice.mean(), precision.mean(), recall.mean(), specificity.mean()
```

## 참고. Dice Loss

3D medical image segmentation task를 수행할 때 DSC를 1에서 뺀 값($ Dice Loss = 1 - DSC $)인 Dice Loss를 사용하여 모델을 최적화 할 수 있다.

---

참고.
[1] [Minaee, S., Boykov, Y., Porikli, F., Plaza, A., Kehtarnavaz, N., & Terzopoulos, D. (2021). Image segmentation using deep learning: A survey. IEEE transactions on pattern analysis and machine intelligence, 44(7), 3523-3542.](https://arxiv.org/abs/2001.05566)<br>
[2] [분류 모델 성능 평가 지표(Accuracy, Precision, Recall, F1 score 등)](https://white-joy.tistory.com/9)<br>
[3] [딥러닝 Segmentation(6) - segmentation 평가(Pixel Accuracy, Mean IOU)](https://velog.io/@cha-suyeon/%EB%94%A5%EB%9F%AC%EB%8B%9D-Segmentation5-segmentation-%ED%8F%89%EA%B0%80Pixel-Accuracy-Mask-IOU)<br>
[4] [Milletari, F., Navab, N., & Ahmadi, S. A. (2016, October). V-net: Fully convolutional neural networks for volumetric medical image segmentation. In 2016 fourth international conference on 3D vision (3DV) (pp. 565-571). Ieee.](https://arxiv.org/abs/1606.04797)
