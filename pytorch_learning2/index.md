# pytorch学习2


## Loss损失函数与反向传播
### loss 损失函数
* 描述输出与真实值之间的差距
* 为更新输出提供一定的证据
### L1Loss
* 衡量Groud Truth 和 output 之间的平均绝对误差
* MAE（Mean Absolute Error）平均绝对误差
### MSELoss
* MSE Mean square Error 平局平方误差
### CrossEntropyLoss 交叉熵误差， 适用于分类问题
* 输入参数：target 真实分类数、x 每一个类别的可能性数组
* EG：target = 0   在第0,1,2三个类别中属于第1类；x = [0.2, 0.3, 0.1]  (对应每一种类别的可能性)
### 反向传播
* 计算完loss之后反向传播, 计算每个节点梯度
* 每一次计算出一个每一个数据计算出一个loss，就会对应一个梯度；然后根据梯度进行下降，更新参数节点，达到使得loss 越来越小的机制

### 各种loss源码测试
```python
import torch

from torch import nn

input = torch.tensor([1, 2, 3], dtype=float)
target = torch.tensor([1, 2, 5], dtype=float)

input = torch.reshape(input, (1, 1, 1, 3))
target = torch.reshape(target, (1, 1, 1, 3))

# loss = nn.L1Loss(reduction="sum")
loss_mae = nn.L1Loss()
loss_mse = nn.MSELoss()

output_mae = loss_mae(input, target)
output_mse = loss_mse(input, target)

print(output_mae)
print(output_mse)

```

### loss 训练测试
```python
import torchvision
from torch import nn
from torch.nn import Sequential, Conv2d, MaxPool2d
from torch.nn import Flatten, Linear, CrossEntropyLoss
from torch.utils.data import DataLoader

# 准备数据集
# 代码在src中， 把数据集下载到 "../data"， 也就是退到上一层目录， 新建一个目data
dataset = torchvision.datasets.CIFAR10("../data", train=False, transform=torchvision.transforms.ToTensor(),
                                       download=True)

# 将数据转换为tensor数据类型 一个batch为64
dataloader = DataLoader(dataset=dataset, batch_size=1)


class SwaggerCifar(nn.Module):
    def __init__(self):
        super(SwaggerCifar, self).__init__()
        self.model1 = Sequential(
            Conv2d(in_channels=3, out_channels=32, kernel_size=5,
                   stride=1, padding=2),
            MaxPool2d(kernel_size=2),
            Conv2d(in_channels=32, out_channels=32, kernel_size=5,
                   stride=1, padding=2),
            MaxPool2d(kernel_size=2),
            Conv2d(in_channels=32, out_channels=64, kernel_size=5,
                   stride=1, padding=2),
            MaxPool2d(kernel_size=2),
            Flatten(),
            Linear(in_features=1024, out_features=64),
            Linear(in_features=64, out_features=10)
        )

    def forward(self, x):
        x = self.model1(x)
        return x


swagger = SwaggerCifar()
# print(swagger)

# 创建loss函数
loss = CrossEntropyLoss()

# 从dataloader中取数据
for data in dataloader:
    imgs, targets = data
    outputs = swagger(imgs)

    loss_result = loss(input=outputs, target=targets)
    loss_result.backward()
    # print(outputs)
    # print(targets)
    print(loss_result)

```

## optim优化器使用
### 原理
* 在求出loss之后执行backward
* 得到每个节点的梯度（gradient）
* 然后使用优化器进行优化

### 常用的优化器及其参数
* Adam optimizer
* SGD optimizer
* 参数：模型参数、学习率LR、momentum

### SGD optimizer
* 在循环外定义一个优化器
* 型中上一步计算的梯度进行清零
* 输入经过网络
* 计算loss
* 反向传播计算梯度
* 梯度下降法更新梯度

### 学习率设置
* 太大容易模型优化不稳定
* 太小模型一直是保持正向优化， 但是优化速度过慢
* 应该设置一个合适的学习率
* 或者开始的时候设置一个大的学习率，在后期设置一个小的学习率

### 源码测试
```python
import torchvision
from torch import nn, optim
from torch.nn import Sequential, Conv2d, MaxPool2d
from torch.nn import Flatten, Linear, CrossEntropyLoss
from torch.utils.data import DataLoader

# 准备数据集
# 代码在src中， 把数据集下载到 "../data"， 也就是退到上一层目录， 新建一个目data
dataset = torchvision.datasets.CIFAR10("../data", train=False, transform=torchvision.transforms.ToTensor(),
                                       download=True)

# 将数据转换为tensor数据类型 一个batch为64
dataloader = DataLoader(dataset=dataset, batch_size=64)


class SwaggerCifar(nn.Module):
    def __init__(self):
        super(SwaggerCifar, self).__init__()
        self.model1 = Sequential(
            Conv2d(in_channels=3, out_channels=32, kernel_size=5,
                   stride=1, padding=2),
            MaxPool2d(kernel_size=2),
            Conv2d(in_channels=32, out_channels=32, kernel_size=5,
                   stride=1, padding=2),
            MaxPool2d(kernel_size=2),
            Conv2d(in_channels=32, out_channels=64, kernel_size=5,
                   stride=1, padding=2),
            MaxPool2d(kernel_size=2),
            Flatten(),
            Linear(in_features=1024, out_features=64),
            Linear(in_features=64, out_features=10)
        )

    def forward(self, x):
        x = self.model1(x)
        return x


swagger = SwaggerCifar()
# print(swagger)

# 创建loss函数
loss = CrossEntropyLoss()

# 定义optimizer
optimizer = optim.SGD(swagger.parameters(), lr=0.01)

# 从dataloader中取数据
for epoch in range(20):
    loss_per_epoch = 0.0
    for data in dataloader:
        # 清零上一个循环计算的梯度grad
        optimizer.zero_grad()

        # 取数据
        imgs, targets = data

        # 数据输入进神经网络
        outputs = swagger(imgs)

        # 计算loss
        loss_result = loss(input=outputs, target=targets)

        # 反向传播计算梯度
        loss_result.backward()

        # 优化器根据梯度对网络参数进行更新（step）
        optimizer.step()

        loss_per_epoch = loss_per_epoch + loss_result
        # print(outputs)
        # print(targets)
        # print(loss_result)
    print(loss_per_epoch)


```










## 完整模型训练验证源码
```python
# -*- coding:utf-8 -*-

import torch
import torchvision
from torch import nn, optim
from torch.nn import Sequential, Conv2d, MaxPool2d
from torch.nn import Flatten, Linear, CrossEntropyLoss
from torch.utils.data import DataLoader
from torch.utils.tensorboard import SummaryWriter

gpu_device = 7

train_data = torchvision.datasets.CIFAR10("../data_cifar10", train=True, transform=torchvision.transforms.ToTensor(),
                                          download=True)

test_data = torchvision.datasets.CIFAR10("../data_cifar10", train=False, transform=torchvision.transforms.ToTensor(),
                                         download=True)

train_data_size = train_data.__len__()
test_data_size = test_data.__len__()

print("train dataset length: {}".format(train_data_size))
print("test dataset length: {}".format(test_data_size))

batchsize = 64
train_dataloader = DataLoader(train_data, batch_size=batchsize)
test_dataloader = DataLoader(test_data, batch_size=batchsize)


class SwaggerCifar(nn.Module):
    def __init__(self):
        super(SwaggerCifar, self).__init__()
        self.module1 = Sequential(
            Conv2d(in_channels=3, out_channels=32, kernel_size=5,
                   stride=1, padding=2),
            MaxPool2d(kernel_size=2),
            Conv2d(in_channels=32, out_channels=32, kernel_size=5,
                   stride=1, padding=2),
            MaxPool2d(kernel_size=2),
            Conv2d(in_channels=32, out_channels=64, kernel_size=5,
                   stride=1, padding=2),
            MaxPool2d(kernel_size=2),
            Flatten(),
            Linear(in_features=1024, out_features=64),
            Linear(in_features=64, out_features=10)
        )

    def forward(self, x):
        x = self.module1(x)
        return x


swagger = SwaggerCifar()

if torch.cuda.is_available():
    swagger = swagger.cuda(gpu_device)

loss = CrossEntropyLoss()

if torch.cuda.is_available():
    loss = loss.cuda(gpu_device)

learing_rate = 0.01
optimizer = optim.SGD(swagger.parameters(), learing_rate)

writer = SummaryWriter("../logs_cifar")

total_train_step = 0

total_test_step = 0

epoch = 2

for i in range(epoch):

    print("------the {}th epoch------".format(i + 1))
    total_train_loss = 0.0

    swagger.train()
    for data in train_dataloader:

        imgs, targets = data
        if torch.cuda.is_available():
            imgs = imgs.cuda(gpu_device)
            targets = targets.cuda(gpu_device)

        outputs = swagger(imgs)

        loss_result = loss(input=outputs, target=targets)

        optimizer.zero_grad()

        loss_result.backward()

        optimizer.step()

        total_train_step = total_train_step + 1

        total_train_loss = total_train_loss + loss_result

        if total_train_step % 50 == 0:
            print("train step: {}, loss: {}".format(total_train_step, loss_result.item()))
            writer.add_scalar("train_loss", loss_result.item(), total_train_step)

    swagger.eval()
    total_test_loss = 0.0
    total_accuracy = 0.0
    with torch.no_grad():
        for data in test_dataloader:

            imgs, targets = data
            if torch.cuda.is_available():
                imgs = imgs.cuda(gpu_device)
                targets = targets.cuda(gpu_device)

            outputs = swagger(imgs)

            loss_result = loss(input=outputs, target=targets)

            total_test_loss = total_test_loss + loss_result.item()

            preds = outputs.argmax(1)
            accuracy = (preds == targets).sum()

            total_accuracy = total_accuracy + accuracy.item()

    print("total test loss: {}".format(total_test_loss))
    print("total accuracy: {}".format(total_accuracy / test_data_size))

    writer.add_scalar("test_loss", total_test_loss, total_test_step)
    writer.add_scalar("test_accuracy", total_accuracy / test_data_size, total_test_step)

    total_test_step = total_test_step + 1

    torch.save(swagger, "../models/swagger_cifar_{}.pth".format(i))
    print("The model has been saved")

writer.close()

```

