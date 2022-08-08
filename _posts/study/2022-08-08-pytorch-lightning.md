---
title: "[머신러닝] PyTorch Lightning"
categories: cv-and-ml
tags: python machine-learning pytorch
toc: true
toc_sticky: true
---

`TorchIO`를 이용해서 3D Image Augmentation 하는 코드 예시를 찾다가 `PyTorch Lightning`[1]이라는 라이브러리를 알게 됐다.<br>

## PyTorch Lightning

`PyTorch Lightning`은 `PyTorch` 프레임워크를 보다 효율적으로 사용할 수 있도록 만들어진 라이브러리다.<br>
Lightning 모듈을 사용하면 코드가 훨씬 직관적이고 간결하다는 장점이 있다.

PyTorch Lightning을 사용했을 때와 사용하지 않았을 때의 코드를 비교해 보자.<br>
먼저 아래의 코드는 PyTorch Lightning을 안 쓰고 training과 validation을 구현한 것이다.

```python
# Training
def train(model, train_loader, device, lr=0.0001, optim_class=optim.Adam, scheduler=None):
  criterion = nn.L1Loss()
  optimizer = optim_class(model.parameters(), lr=lr)
  total_loss = 0

  model.train()
  for inputs, labels in tqdm(train_loader):
    inputs, labels = inputs.to(device, dtype=torch.float), labels.to(device, dtype=torch.float)

    optimizer.zero_grad()

    output = model(inputs)

    loss = criterion(output, labels)
    loss.backward()

    optimizer.step()
    if scheduler:
      scheduler.step()

    total_loss += loss.data.item()

    gc.collect()
    torch.cuda.empty_cache()

  return total_loss
```

```python
# Validation
def valid(model, valid_loader, device):
  criterion = nn.L1Loss()
  total_loss = 0

  model.eval()
  with torch.no_grad():
    for inputs, labels in tqdm(valid_loader):
      inputs, labels = inputs.to(device, dtype=torch.float), labels.to(device, dtype=torch.float)

      output = model(inputs)

      loss = criterion(output, labels)
      total_loss += loss.data.item()

  return total_loss
```

```python
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
n_epoch = 100
batch_size = 16
lr = 0.0001
optim_class = optim.Adam

dataset = CustomDataset()
train_set, valid_set = train_test_split(dataset, test_size=0.2)
train_loader = DataLoader(train_set, batch_size=batch_size)
valid_loader = DataLoader(valid_set, batch_size=batch_size)

model = Net()
model.to(device)
for epoch in range(n_epoch):
  print('# Epoch %d / %d' % (epoch + 1, n_epoch))
  train_mae = train(model, train_loader, device, lr=lr, optim_class=optim_class)
  valid_mae = valid(model, valid_loader, device)
  print('Train MAE: %.3f / Validation MAE: %.3f' % (train_mae, valid_mae))
```

이 코드가 `PyTorch Lightning`을 쓰면 이렇게 깔끔해진다:

```python
import pytorch_lightning as pl
...

data = CustomDataModule(batch_size=16, train_val_ratio=0.2)

net = Net()

model = Model(
  net=net,
  criterion=nn.L1Loss(),
  learning_rate=0.0001,
  optimizer_class=optim.Adam
)

trainer = pl.Trainer(
  gpus=1 if torch.cuda.is_available() else 0
)

trainer.fit(model=model, datamodule=data)
```

### 더 자세히 살펴보기

#### 데이터 모듈

`PyTorch Lightning`의 또다른 장점은 데이터 모듈을 통해 data augmentation도 손쉽게 구현할 수 있다는 점이다 [2].

```python
# augmentation 라이브러리
import torchio as tio
...

class CustomDataModule(pl.LightningDataModule):
  def __init__(self, batch_size, train_val_ratio):
    super().__init__()
    self.prepare_data_per_node = True
    self.batch_size = batch_size
    self.train_val_ratio = train_val_ratio
    self.train_set = None
    self.valid_set = None

  def get_augmentation_transform(self):
    random_rotate = ...
    random_flip = ...
    random_shift = ...
    augment = tio.transforms.OneOf([random_rotate, random_flip, random_shift])
    return augment

  def prepare_data(self):
    ...
    train, val = train_test_split(dataset, test_size=self.train_val_ratio)
    augment = self.get_augmentation_transform()

    self.train_set = tio.SubjectsDataset(train, transform=augment)
    self.valid_set = tio.SubjectsDataset(val)

  def train_dataloader(self):
    return DataLoader(self.train_set, batch_size=self.batch_size)

  def val_dataloader(self):
    return DataLoader(self.valid_set, batch_size=self.batch_size)
```

#### 학습 루프 구조

일반적으로 쓰는 구조는 `training_step` - `validation_step` - `validation_epoch_end`이다 [3].

```python
class Model(pl.LightningModule):
  def __init__(self, net, criterion, learning_rate, optimizer_class):
    super().__init__()
    self.net = net
    self.lr = learning_rate
    self.criterion = criterion
    self.optimizer_class = optimizer_class

  def configure_optimizers(self):
    optimizer = self.optimizer_class(self.parameters(), lr=self.lr)
    return optimizer

  def prepare_batch(self, batch):
    device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
    return batch['X'][tio.DATA].to(device, dtype=torch.float), batch['y'].to(device, dtype=torch.float)

  def infer_batch(self, batch):
    X, y = self.prepare_batch(batch)
    y_hat = self.net(X)
    return y_hat, y

  def training_step(self, batch, batch_idx):
    y_hat, y = self.infer_batch(batch)
    loss = self.criterion(y_hat, y)
    self.log('train_loss': loss)
    return loss

  def validation_step(self, batch, batch_idx):
    y_hat, y = self.infer_batch(batch)
    loss = self.criterion(y_hat, y)
    self.log('val_loss': loss)
    return loss

  def validation_epoch_end(self, outputs):
        # called at the end of the validation epoch
        # outputs is an array with what you returned in validation_step for each batch
        # outputs = [{'loss': batch_0_loss}, {'loss': batch_1_loss}, ..., {'loss': batch_n_loss}]
        avg_loss = torch.stack([x['val_loss'] for x in outputs]).mean()
        tensorboard_logs = {'val_loss': avg_loss}
        return {'avg_val_loss': avg_loss, 'log': tensorboard_logs}
```

#### WandB 연동

`WandB`는 실험 모니터링을 제공하는 머신러닝 플랫폼이다 [4].
`PyTorch Lightning`에는 `WandbLogger` 모듈이 있어서 `WandB`에 쉽게 연동할 수 있다.

```python
# logger 세팅
from pytorch_lightning.loggers import WandbLogger
wandb_logger = WandbLogger(name='experiment-001', project='pytorch-lightning')

...

trainer = pl.Trainer(
  logger=wandb_logger,  # logger 사용
  gpus=1 if torch.cuda.is_available() else 0
)
```

### `PyTorch Lightning` 사용 후기

확실히 Lightning 모듈을 사용하니까 나중에 코드를 읽거나 수정할 때도 편하고 online augmentation을 구현하기도 쉽다는 점에서 이게 좋은 툴이라고 느꼈다.
하지만 `max_epochs` 값을 설정했는데도 설정이 안 됐다는 warning이 뜨면서 기본값인 1000으로 설정되는 등의 버그가 있어서 그런 건 좀 아쉬웠다. (아님 내가 설정 방법을 잘못 이해한 건지...)<br>
잘 활용하면 좋을 것 같은데 좀 더 공부하고 내 능력만 된다면 이 라이브러리에 contribute 하고 싶다.

## 참고 문서 출처

[1] [PyTorch Lightning Github](https://github.com/Lightning-AI/lightning)<br>
[2] [TorchIO를 이용한 3D Segmentation](https://blog.promedius.ai/torchioreul-iyonghan-3d-segmentation/)<br>
[3] [우리가 PyTorch Lightning을 써야 하는 이유](https://baeseongsu.github.io/posts/pytorch-lightning-introduction/)<br>
[4] [Weights & Biases Documentation](https://docs.wandb.ai/)<br>
[Pytorch lightning 튜토리얼](https://www.secmem.org/blog/2021/01/07/pytorch-lightning-tutorial/)<br>
[PyTorch Lightning](https://visionhong.tistory.com/30)
