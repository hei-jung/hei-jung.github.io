---
title: "[머신러닝] pytorch loss function 정리"
categories: machine-learning
tags: python ML pytorch
---


## MSELoss

> Creates a criterion that mesures the mean squared error between each element in the input x and target y.

mean squared error를 구하는 loss function으로, **regression predictive modeling problem**에서 쓰면 좋다. input shape과 target shape이 (N,*)으로 똑같아야 한다.


## CrossEntropyLoss

> It is useful when training a classification problem with C classes. If provided, the optional argument weight should be a 1D Tensor assigning weight to each of the classes. This is particularly useful when you have an unbalanced training set.

**C개의 class가 있는, 즉 class가 여러 개인 classification problem**에서 쓸 수 있는 loss function이다. input은 (N,C) 형태, target은 (N) 형태여야 한다.

형태가 맞지 않으면 에러가 발생하기 때문에 요구하는 형태가 아니라면 미리 reshape을 통해 맞춰줘야 한다.


## BCELoss

> Creates a criterion that measures the Binary Cross Entropy between the target and the output.

**2개의 class만 있는 binary case**에 사용할 수 있는 loss function이다. input은 0~1 사이 값, target은 0 또는 1을 갖게 된다. input과 target shape이 (N,*)으로 똑같아야 한다.



참고. [PyTorch documentation](https://pytorch.org/docs/stable/nn.html)<br>
참고. [How to Choose Loss Functions When Training Deep Learning Neural Networks](https://machinelearningmastery.com/how-to-choose-loss-functions-when-training-deep-learning-neural-networks/)
