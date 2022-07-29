---
title: "[머신러닝] Online augmentation"
categories: cv-and-ml
tags: python machine-learning pytorch
toc: true
toc_sticky: true
---

Online data augmentation에 대해서 알아보자.<br>

## Data Augmentation이란?

Data Augmentation (데이터 증강)이란 **원래 가지고 있는 데이터에 변화 (transformation)를 줘서** 새로운 데이터를 생성함으로써 **데이터 개수를 늘리는 기법**을 말한다.
대표적으로 회전, 이동, 스케일링 등의 affine transform이나 flip, crop 같은 것들이 augmentation 기법에 해당한다.<br>
Augmentation을 통해 데이터 양을 늘려서 학습할 수 있기 때문에 overfitting을 줄일 수 있고 generalization에 도움이 된다.<br>
이게 가능한 이유는 컴퓨터는 데이터가 조금만 달라져도 완전히 다른 데이터로 인식하기 때문이다.

![Figure 1](/assets/images/220728/Fig_01.png)<span style="font-size:xx-small">[1]</span>

예를 들어 위의 이미지를 우리 사람의 눈으로 봤을 땐 조금씩 회전하거나 좌우반전만 시킨 똑같은 고양이 사진으로 보이지만 컴퓨터의 입장에서 영상 데이터는 **숫자**이기 때문에 전혀 다른 데이터로 보는 것이다.

## Offline Augmentation vs. Online Augmentation

### Offline (pre-processing) Augmentation

offline augmentation은 학습 전 데이터 전처리 단계에서 augmentation을 해주는 것으로, training 데이터셋의 규모가 작을 때 주로 사용하는 방법이다 [2].
증강한 데이터를 파일로 따로 저장하고 학습할 때 불러오는 방식이 offline augmentation에 해당한다.

### Online (real-time) Augmentation

학습 도중에 mini-batch에다가 augmentation을 적용하는 방법이다.
즉, 이 방법을 사용하면 학습 모델이 매 epoch마다 다른 데이터를 학습하게 된다.
offline augmentation랑 비교해보면 오프라인에서는 생성된 데이터가 처음부터 training set의 일부이므로 같은 데이터를 여러 번 학습한다 [2]. (augmentation 안 했을 때 학습 방식이랑 같다고 보면 됨)<br>
따라서 당연히 online augmentation을 쓰는 것이 offline augmentation을 쓰는 것보다 generalization에 유리하다. 이런 이유로 요즘은 online augmentation을 많이 적용하는 추세이다.

## 3D 영상 Augmentation

내가 연구에 사용하고 있는 데이터는 3D 영상 데이터이므로 3D Image Augmentation 방법을 찾아봤다.

### 1. [TorchIO](https://discuss.pytorch.org/t/torchio-new-library-for-efficient-3d-data-augmentation-and-patch-based-training/65902), [TorchIO github](https://github.com/fepegar/torchio)

#### 사용법

```python
# load the library
import torchio as tio
```

```python
# Rotation
# scales: scaling 비율
# degrees: 회전 각도
random_rotate = tio.RandomAffine(scales=(1.0, 1.0), degrees=12,)
transformed_img = random_rotate(img)
```

```python
# LR (lateral) Flip
# axes: 기준면
random_flip = tio.RandomFlip(axes='LR')
transformed_img = random_flip(img)
```

```python
# Shifting (Translation)
# translation: (x,y,z) 이동 범위
random_shift = tio.RandomAffine(scales=(1.0, 1.0), degrees=0, translation=(20,20,20))
transformed_img = random_shift(img)
```

여러 종류의 transformation을 한 번에 적용하거나 하나를 골라서 적용하는 방법도 있다 [3].

```python
transforms_list = [random_rotate, random_flip, random_shift]
# Composition
# compose several transforms together
transform_compose = tio.transforms.Compose(transforms_list)
transformed_img = transform_compose(img)
# apply one of the given transforms
transform_oneof = tio.transforms.OneOf(transforms_list)
transformed_img = transform_oneof(img)
```

### 2. [MONAI](https://discuss.pytorch.org/t/medical-open-network-for-ai/85700), [MONAI github](https://github.com/Project-MONAI/MONAI/tree/dev/monai/transforms)

```python
# load the library
from monai import transforms
```

```python
# Rotation
# range_y: 회전 각도 범위
# padding_mode: grid를 벗어난 부분을 채울 값
random_rotate = transforms.RandRotate(prob=1, range_y=[0.4, 0.4], padding_mode="zeros")
transformed_img = random_rotate(img)
```

```python
# Flip
# spatial_axis: 반전시킬 면
random_flip = transforms.RandFlip(prob=1, spatial_axis=0)
transformed_img = random_flip(img)
```

```python
# Translation
# translate_range: 이동 범위 (pixel/voxel 단위)
# padding_mode: grid를 벗어난 부분을 채울 값
random_shift = transforms.RandAffine(prob=1, translate_range=, padding_mode='zero')
transformed_img = random_shift(img)
```

### 3. [SimpleITK](https://simpleitk.org/SPIE2019_COURSE/03_data_augmentation.html), [SimpleITK-Notebooks](https://github.com/InsightSoftwareConsortium/SimpleITK-Notebooks/blob/master/Python/70_Data_Augmentation.ipynb)

## `TorchIO`를 이용한 Online Augmentation

추가 예정


## 참고 문서 출처

[1] [Data Augmentation | How to use Deep Learning when you have Limited Data — Part 2](https://nanonets.com/blog/data-augmentation-how-to-use-deep-learning-when-you-have-limited-data-part-2/)<br>
[2] [Data Augmentation techniques in python](https://towardsdatascience.com/data-augmentation-techniques-in-python-f216ef5eed69)<br>
[3] [TorchIO docs](https://torchio.readthedocs.io/transforms/augmentation.html#composition)
