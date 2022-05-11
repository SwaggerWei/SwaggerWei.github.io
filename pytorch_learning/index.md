# pytorch学习1


## 卷机层的使用
### 准备数据集
* 定义自己的数据集：做必要的transform等
* 定义自己数据集的data、label
```python
dataset = torchvision.datasets.CIFAR10("../data_cifar10", train=False, transform=torchvision.transforms.ToTensor(),
                                       download=False)
```

### 根据数据集准备dataloader
* 输入batchsize， dataset等参数
```python
dataloader = DataLoader(dataset=dataset, batch_size=64)
```

### 定义自己的网络结构
* 继承父辈的init函数
* 定义自己的网络结构-卷积层：输入通道数、输出通道数、卷机核大小等参数
* 定义前向传播：依次前向顺序调用自己的各个层
```python
class SwaggerConv(nn.Module):
    def __init__(self):
        super(SwaggerConv, self).__init__()
        self.conv1 = Conv2d(in_channels=3, out_channels=6, kernel_size=3, stride=1, padding=0)

    # 前向传播， 输入为x， 输出为y
    def forward(self, x):
        y = self.conv1(x)
        return y
```

### 后续操作
* 实例化网络结构
* 通过dataloader 取数据
* 将取出的数据 输送入网络结构当中 前向传播得到输出
* 将输出写入到tensorboard日志当中
```python
swagger = SwaggerConv()

writer = SummaryWriter("../logs")

step = 0
for data in dataloader:
    imgs, targets = data
    output = swagger(imgs)

    # 原图写入到tensorboard当中
    writer.add_images("input", img_tensor=imgs, global_step=step)

    # 经过卷积之后的图像
    # output为6通道，无法在tensorboard中以图片的格式显示，
    # 将其使用reshape 改成三通道（不严谨操作方式）
    output = torch.reshape(output, (-1, 3, 30, 30))
    writer.add_images("output", img_tensor=output, global_step=step)
    step = step + 1

writer.close()
```

### 测试源码
```python
# 卷积层的使用 torch.nn.Conv2d

import torch
import torchvision
from torch.utils.data import DataLoader
from torch import nn
from torch.nn import Conv2d
from torch.utils.tensorboard import SummaryWriter

# 准备数据集
# 代码在src中， 把数据集下载到 "../data"， 也就是退到上一层目录， 新建一个目data
# dataset = torchvision.datasets.CIFAR10("../data", train=False, transform=torchvision.transforms.ToTensor(),
#                                        download=True)
# 不下载该数据集download=False，数据集在本地的路径为../data_cifar10，
dataset = torchvision.datasets.CIFAR10("../data_cifar10", train=False, transform=torchvision.transforms.ToTensor(),
                                       download=False)
# 将数据转换为tensor数据类型 一个batch为64
dataloader = DataLoader(dataset=dataset, batch_size=64)


# 定义自己的网络结构， 输入通道为3， 输出通道为6（意味着有两个卷积核）， 卷积核的大小为3， 步长为1， 不增加padding
class SwaggerConv(nn.Module):
    def __init__(self):
        super(SwaggerConv, self).__init__()
        self.conv1 = Conv2d(in_channels=3, out_channels=6, kernel_size=3, stride=1, padding=0)

    # 前向传播， 输入为x， 输出为y
    def forward(self, x):
        y = self.conv1(x)
        return y


swagger = SwaggerConv()

writer = SummaryWriter("../logs")

step = 0
for data in dataloader:
    imgs, targets = data
    output = swagger(imgs)

    # 原图写入到tensorboard当中
    writer.add_images("input", img_tensor=imgs, global_step=step)

    # 经过卷积之后的图像
    # output为6通道，无法在tensorboard中以图片的格式显示，
    # 将其使用reshape 改成三通道（不严谨操作方式）
    output = torch.reshape(output, (-1, 3, 30, 30))
    writer.add_images("output", img_tensor=output, global_step=step)
    step = step + 1

writer.close()

```

## 最大池化层使用
### 最大池化-取窗口中的最大值
* MaxPool2d   --- 最大池化 （下采样）
* MaxUnPool2d --- 最大池化 （上采样）

### 参数使用
* kernel_size：核  窗口大小 the size of the window to take a max over
* stride：步长 the stride of the window. Default value is kernel_size
* padding：边缘补零 implicit zero padding to be added on both sides
* dilation：核的数字之间是否紧密相连 a parameter that controls the stride of elements in the window
* ceil_mode：是使用ceil还是floor 模式进行计算 （ceil-》不足填充整个窗口丢弃）

### 源码测试
```python
# 最大池化层的使用
#     非线性激活的使用
#     sigmoid 层
#     relu 层

import torch
import torchvision
from torch.utils.data import DataLoader
from torch import nn
from torch.nn import Conv2d
from torch.nn import MaxPool2d
from torch.nn import ReLU
from torch.nn import Sigmoid

from torch.utils.tensorboard import SummaryWriter

# 准备数据集
# 代码在src中， 把数据集下载到 "../data"， 也就是退到上一层目录， 新建一个目data
dataset = torchvision.datasets.CIFAR10("../data", train=False, transform=torchvision.transforms.ToTensor(),
                                       download=True)

# 将数据转换为tensor数据类型 一个batch为64
dataloader = DataLoader(dataset=dataset, batch_size=64)


# 定义自己的网络结构， 输入通道为3， 输出通道为6（意味着有两个卷积核）， 卷积核的大小为3， 步长为1， 不增加padding
class SwaggerMaxPool(nn.Module):
    def __init__(self):
        super(SwaggerMaxPool, self).__init__()
        self.conv1 = Conv2d(in_channels=3, out_channels=6, kernel_size=3, stride=1, padding=0)
        self.maxpool1 = MaxPool2d(kernel_size=3, stride=3, padding=0)
        self.relu1 = ReLU()
        self.sigmoid1 = Sigmoid()

    # 前向传播， 输入为x， 输出为y
    def forward(self, x):
        # y = self.conv1(x)
        # y = self.maxpool1
        # y = self.relu1(x)
        y = self.sigmoid1(x)
        return y


swagger = SwaggerMaxPool()

writer = SummaryWriter("../logs")

step = 0
for data in dataloader:
    imgs, targets = data
    output = swagger(imgs)
    # 原图写入到tensorboard当中
    writer.add_images("input", img_tensor=imgs, global_step=step)

    writer.add_images("output", img_tensor=output, global_step=step)
    step = step + 1

writer.close()

```

## 非线性激活  
* nn.ReLU 对非0数字进行截断，其中的参数inplace， 如果为TRUE，则对input 进行原地替换操作，默认为False，不进行原地操作。
* nn.Sigmoid 
* 使得模型能够使用各种非线性结构的特征，增强模型的泛化能力

## 线性层和及其他层
* Normalization Layers：标准化层：映射到指定的区间，可以增加训练的速度
* Recurrent Layers：文字识别网络结构
* Transformer Layers：特定的网络结构（paper： attention is all you need）
* Dropout Layers：满足服从B-p分布的特性将输入中的数据随机置零---->防止过拟合（Efficient Object Localization Using Convolutional networks）
* Sparse Layers：自然语言处理（NLP）网络
* Distance Functions：计算距离的函数
* Loss Function：计算loss
* Linear Layers：线性层，全连接层 fully connected layers

## sequential和网络搭建实战
### 不使用sequential
* init 当中定义网络结构
* forward当中按照顺序调用各层网络

### 使用sequential
* init 使用sequential定义model 在model中按照顺序添加网络结构
* forward 中直接调用model

### 测试网络是否正确小技巧
* torch.ones / torch.zeros 生成input
* 输入到网络当中看是否出错

### 源码测试
```python
import torch
import torchvision
from torch.nn import Flatten
from torch.utils.data import DataLoader
from torch import nn
from torch.nn import Conv2d
from torch.nn import MaxPool2d
from torch.nn import ReLU
from torch.nn import Sigmoid
from torch.nn import Linear
from torch.nn import Sequential
from collections import OrderedDict

from torch.utils.tensorboard import SummaryWriter

# 准备数据集
# 代码在src中， 把数据集下载到 "../data"， 也就是退到上一层目录， 新建一个目data
dataset = torchvision.datasets.CIFAR10("../data", train=False, transform=torchvision.transforms.ToTensor(),
                                       download=True)

# 将数据转换为tensor数据类型 一个batch为64
dataloader = DataLoader(dataset=dataset, batch_size=64)


class SwaggerCifar(nn.Module):
    def __init__(self):
        super(SwaggerCifar, self).__init__()
        # self.conv1 = Conv2d(in_channels=3, out_channels=32, kernel_size=5,
        #                     stride=1, padding=2)
        # self.maxpool1 = MaxPool2d(kernel_size=2)
        # self.conv2 = Conv2d(in_channels=32, out_channels=32, kernel_size=5,
        #                     stride=1, padding=2)
        # self.maxpool2 = MaxPool2d(kernel_size=2)
        # self.conv3 = Conv2d(in_channels=32, out_channels=64, kernel_size=5,
        #                     stride=1, padding=2)
        # self.maxpool3 = MaxPool2d(kernel_size=2)
        # self.flatten = Flatten()
        # self.linear1 = Linear(in_features=1024, out_features=64)
        # self.linear2 = Linear(in_features=64, out_features=10)

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
        # x = self.conv1(x)
        # x = self.maxpool1(x)
        # x = self.conv2(x)
        # x = self.maxpool2(x)
        # x = self.conv3(x)
        # x = self.maxpool3(x)
        # x = self.flatten(x)
        # x = self.linear1(x)
        # x = self.linear2(x)

        x = self.model1(x)

        return x


swagger = SwaggerCifar()

# 使用torch的数字生成器简单检验网络的输入输出是否正确
input = torch.ones(64, 3, 32, 32)
output = swagger(input)
# print(output[0])
print(output.shape)
print(swagger)

# tensorboard 网络结构的可视化
writer = SummaryWriter("../logs")
writer.add_graph(swagger, input)
writer.close()

```
