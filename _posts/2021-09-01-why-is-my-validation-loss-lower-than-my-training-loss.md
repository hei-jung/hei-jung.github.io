---
title: "[머신러닝] validation loss가 training loss보다 작게 나오는 이유!?"
categories: machine-learning
tags: python ML
---

원래 학습 모델을 돌리면 학습 데이터에 대한 손실(training loss)보다 검증 데이터에 대한 손실(validation loss)이 더 큰 게 일반적이다. 근데 어느 날 내 모델은 validation loss가 더 작다는 사실을 발견했다.

![image](https://user-images.githubusercontent.com/40985307/131801703-5c7eb604-88de-4ee7-ae37-65035afcedaf.png)



그래서 그 이유를 찾기 위해 구글링을 해봤다: [참고링크](https://www.pyimagesearch.com/2019/10/14/why-is-my-validation-loss-lower-than-my-training-loss/)

<br>
위의 글 작성자도 나와 같은 경험을 했고, 이렇게 되는 이유에 대해 생각해 봤다고 한다.
<br>



- **그래프로 나타낼 때 training loss와 validation loss를 반대로 실수했나?**

: 가능성 있음. 작성자는 matplotlib 같은 라이브러리를 따로 쓰지 않고 CSV 파일에 loss를 저장하고 엑셀에 그래프를 그렸다. 분명히 실수를 일으킬 만하다.

- **코드에 버그가 있었나?**

: 거의 확실함. 작성자는 자바와 머신러닝을 동시에 독학하고 있었기에 정신 없어서 머신러닝 코드에 버그가 있었을 만하다.

- **작성자가 머신러닝에 대해 제대로 이해를 못할 정도로 너무 피곤했나?**

: 역시 꽤 가능성 있음. 그 당시 잠을 거의 안 잤고, 그래서 당연한 걸 놓쳤을 수 있다.


<br>
그런데 저 세 가지 이유 때문이라고 하기엔 validation loss와 training loss의 차이가 막 엄청나게 크진 않았던 모양이다. (나랑 비슷한 상황,,)<br>
결국 작성자는 학교에서 머신러닝을 배우면서 드디어 이유를 알게 되었는데 다음과 같다.<br>



## 1. Regularization(최적화) 적용 - training 할 때는 했는데, validation&testing 할 때는 안 함

Regularization 메서드를 사용하면 검증 정확도를 높이기 위해 학습 정확도가 희생되는 경우가 종종 있다고 한다. 그러다 보니 loss도 역전이 되는 거라고.<br>
validation&testing 할 때에도 regularization loss를 추가해보자.


## 2. training loss는 각 epoch 진행 중에 계산되고, validation loss는 각 epoch이 끝나고 계산됨

머신러닝에서 1 epoch이란 전체 데이터셋에 대해 한 번 학습을 완료시키는 것을 의미한다. 즉 전체 epochs=80이면 전체 데이터셋을 80번 사용해서 학습하는 것이다.<br>
loss 값을 계산할 때 training loss는 각 epoch이 진행되는 도중에 계산되는 반면 validation loss는 각 epoch이 끝나고 나서 계산된다. 이런 경우에 training loss 계산이 먼저 끝나기 때문에 validation loss보다 큰 값이 나오는 것이 당연하다.<br>
그래프에 나타낼 때 training loss 곡선을 왼쪽으로 반 epoch만큼 평행이동 시켜보자.


## 3. validation set이 training set보다 쉬울지도

validation set의 크기가 너무 작거나 제대로 샘플링되지 않은 경우에도 (예를 들어 쉬운 답만 몰려있음) 이 현상이 나타날 수 있다. 이럴 때 체크해봐야 할 것!

```
- validation set이 training set과 같은 분포로 샘플링되었다고 보장할 수 있는가
- validation set이 training set만큼 어렵다고 확신할 수 있는가
- 데이터 누수가 없었다고 확신할 수 있는가 (예를 들어 training 샘플이 validation 샘플에 섞여 들어가진 않았는지 확인해보기)
- 본인의 코드가 전체 데이터셋을 training set과 validation set으로 적절히 나눴다고 자신할 수 있는가
```



## +. 학습을 충분히 빡세게 했는지?

사람들이 머신러닝 할 때 가장 우려하는 부분 중 하나가 과적합(overfitting)인데, 이걸 막으려다 보니 regularization을 너무 과하게 할 때가 있다. (over-regularizing)<br>
본인의 validation loss가 training loss보다 작은 이유가 앞서 언급한 세 가지 중에 해당하지 않는다면 over-regularizing 하진 않았는지 생각해보자.


---

내 생각에 나는 데이터 양이 많지 않아서 그런 것 같다. 나중에 데이터를 더 많이 받게 되면 해결될지도!? 그랬는데도 해결이 안 된다면 모델을 다시 확인해봐야지.
