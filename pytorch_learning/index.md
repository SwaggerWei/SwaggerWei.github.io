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


