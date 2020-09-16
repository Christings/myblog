---
title: PyTorch-3.入门
date: 2020-02-28 20:44:53
tags: PyTorch
categories:
        - 机器学习和深度学习
        - PyTorch
---
PyTorch 是一个基于 Python 的科学计算包，主要定位两类人群：

    NumPy 的替代品，可以利用 GPU 的性能进行计算。
    深度学习研究平台拥有足够的灵活性和速度。
## 一、张量Tensors
Tensors 类似于 NumPy 的 ndarrays ，同时 Tensors 可以使用 GPU 进行计算。

```
from __future__ import print_function
import torch
# x=torch.empty(5,3) # 构造一个5x3矩阵，不初始化
# x=torch.rand(5,3) # 构造一个随机初始化的矩阵
# x=torch.zeros(5,3,dtype=torch.long) # 构造一个全0的矩阵，数据类型为long
x=torch.tensor([5.5,3]) # 构造一个张量，直接使用数据
print(x)
```

```
x=x.new_ones(5,3,dtype=torch.double) # 创建一个 tensor 基于已经存在的 tensor
print(x)
x=torch.randn_like(x,dtype=torch.float) 
print(x)
print(x.size()) # 获取x的维度信息
```

注意：torch.Size 是一个元组，所以它支持左右的元组操作。

```
# 加法
y=torch.rand(5,3)
print(x+y)
print(torch.add(x,y)) 
result=torch.empty(5,3)
torch.add(x,y,out=result) # 提供一个输出tensor为参数
print(result)
y.add_(x) # 加法in-place
print(x)
```

注意：任何使张量会发生变化的操作都有一个前缀 ‘’。例如：x.copy(y), x.t_(), 将会改变 x.

```
print(x[:,1]) # 使用标准的Numpy类似的索引操作
```

```
# 改变大小：如果你想改变一个 tensor 的大小或者形状，你可以使用 torch.view:
x=torch.randn(4,4)
y=x.view(16)
z=x.view(-1,8) # the size -1 is inferred from other dimensions
print(x.size(),y.size(),z.size())  
```

```
x=torch.randn(1)
print(x)
print(x.item())  # 使用.item() 来获得tensor的value 。
```
## 二、自动微分
autograd 包是 PyTorch 中所有神经网络的核心。autograd 软件包为 Tensors 上的所有操作提供自动微分。它是一个由运行定义的框架，这意味着以代码运行方式定义你的后向传播，并且每次迭代都可以不同。我们从 tensor 和 gradients举例：
### 2.1 Tensor

torch.Tensor 是包的核心类。如果将其属性 .requires_grad 设置为 True，则会开始跟踪针对 tensor 的所有操作。完成计算后，您可以调用 .backward() 来自动计算所有梯度。该张量的梯度将累积到 .grad 属性中。

要停止 tensor 历史记录的跟踪，您可以调用 .detach()，它将其与计算历史记录分离，并防止将来的计算被跟踪。

要停止跟踪历史记录（和使用内存），您还可以将代码块使用 with torch.no_grad(): 包装起来。在评估模型时，这是特别有用，因为模型在训练阶段具有 requires_grad = True 的可训练参数有利于调参，但在评估阶段我们不需要梯度。

还有一个类对于 autograd 实现非常重要那就是 Function。Tensor 和 Function 互相连接并构建一个非循环图，它保存整个完整的计算过程的历史信息。每个张量都有一个 .grad_fn 属性保存着创建了张量的 Function 的引用，（如果用户自己创建张量，则g rad_fn 是 None ）。

如果你想计算导数，你可以调用 Tensor.backward()。如果 Tensor 是标量（即它包含一个元素数据），则不需要指定任何参数backward()，但是如果它有更多元素，则需要指定一个gradient 参数来指定张量的形状。

```
import torch
x=torch.ones(2,2,requires_grad=True) # 创建一个张量，设置 requires_grad=True 来跟踪与它相关的计算
print(x)

y=x+2
print(y)

print(y.grad_fn) # y 作为操作的结果被创建，所以它有 grad_fn
```

输出：

    tensor([[1., 1.],
            [1., 1.]], requires_grad=True)
    tensor([[3., 3.],
            [3., 3.]], grad_fn=<AddBackward0>)
    <AddBackward0 object at 0x7f95ccaa0810>

```
z=y*y*3
out=z.mean()
print(z,out)
```

输出：

    tensor([[27., 27.],
        [27., 27.]], grad_fn=<MulBackward0>) tensor(27., grad_fn=<MeanBackward0>)

.requires_grad_( ... ) 会改变张量的 requires_grad 标记。输入的标记默认为 False ，如果没有提供相应的参数。

```
a=torch.randn(2,2)
a=(a*3)/(a-1)
print(a.requires_grad)
a.requires_grad_(True)
print(a.requires_grad)
b=(a*a).sum()
print(b.grad_fn)
```

输出：

    False
    True
    <SumBackward0 object at 0x7fe1db427dd8>

### 2.2 梯度
现在后向传播，因为输出包含了一个标量，out.backward() 等同于out.backward(torch.tensor(1.))。

```
out.backward()
# out.backward(retain_graph=True)
print(x.grad) # 打印梯度 d(out)/dx
```

输出：

    tensor([[4.5000, 4.5000],
        [4.5000, 4.5000]])

```
x=torch.randn(3,requires_grad=True)
y=x*2
while y.data.norm() < 1000:
    y=y*2
print(y)
```

在这种情况下，y 不再是一个标量。torch.autograd 不能够直接计算整个雅可比，但是如果我们只想要雅可比向量积，只需要简单的传递向量给 backward 作为参数。

```
v = torch.tensor([0.1, 1.0, 0.0001], dtype=torch.float)
y.backward(v)

print(x.grad)
```

```
# 将代码包裹在 with torch.no_grad()，来停止对从跟踪历史中 的 .requires_grad=True 的张量自动求导。
print(x.requires_grad)
print((x ** 2).requires_grad)

with torch.no_grad():
    print((x ** 2).requires_grad)
```

## 三、神经网络
神经网络可以通过 torch.nn 包来构建。

现在对于自动梯度(autograd)有一些了解，神经网络是基于自动梯度 (autograd)来定义一些模型。一个 nn.Module 包括层和一个方法 forward(input) 它会返回输出(output)。

数字图片识别的网络：一个简单的前馈神经网络，它接收输入，让输入一个接着一个的通过一些层，最后给出输出。
![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/nn.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/nn.png)

神经网络训练过程:

    1.定义一个包含可训练参数的神经网络

    2.迭代整个输入

    3.通过神经网络处理输入

    4.计算损失(loss)

    5.反向传播梯度到神经网络的参数

    6.更新网络的参数，典型的用一个简单的更新方法：weight = weight - learning_rate *gradient
