---
title: PyTorch-6-卷积神经网络参数解释
date: 2020-04-17 19:05:50
tags: PyTorch
categories:
        - 机器学习和深度学习
        - PyTorch
---
https://www.cnblogs.com/wanghui-garcia/p/10775859.html

https://www.cnblogs.com/wanghui-garcia/p/10775859.html
https://blog.csdn.net/Haiqiang1995/article/details/90300686
https://medium.com/@pkqiang49/%E4%B8%80%E6%96%87%E7%9C%8B%E6%87%82%E5%8D%B7%E7%A7%AF%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C-cnn-%E5%9F%BA%E6%9C%AC%E5%8E%9F%E7%90%86-%E7%8B%AC%E7%89%B9%E4%BB%B7%E5%80%BC-%E5%AE%9E%E9%99%85%E5%BA%94%E7%94%A8-6047fb2add35

https://blog.csdn.net/r1254/article/details/104888502

https://blog.csdn.net/dyk4ever/article/details/102841518
https://blog.csdn.net/weixin_44307764/article/details/102353344
https://blog.csdn.net/yz2zcx/article/details/100187471/



https://www.jianshu.com/p/a2b536945e3c
https://blog.csdn.net/lzy2014/article/details/25916235
https://yq.aliyun.com/articles/373368
https://www.cnblogs.com/luxiaoxun/archive/2012/11/10/2764056.html
https://www.cnblogs.com/fzz9/p/8973315.html
https://www.jianshu.com/p/6f9d99f7ad54
https://blog.csdn.net/weixin_42042680/article/details/80994726
### 1、卷积运算函数

torch.nn.Conv2d的功能是：对由多个输入平面组成的输入信号进行二维卷积。

    layer = nn.Conv2d(in_channels=1,out_channels=3,kernel_size=3,stride=1,padding=0)

    in_channels:
        1）输入通道数，对于图片层一般为1（灰度）3（RGB）
        2）定义一种输入规则，要求上一层的输出必须和这个输入一致，也可以理解为并发in_channels个channel在上一层feature_map(特征映射)上进行卷积运算

    out_channels:
        1)直观理解是输出层通道数，              
        2)换一种理解是kernels（卷积核）个数，其中，每个卷积核会输出局部特征，比如面部中有头发feature，衣服颜色的feature都是由不同的kernel进行卷积运算得到的。

    stride(步长)：控制cross-correlation的步长，可以设为1个int型数或者一个(int, int)型的tuple。

    padding(补0)：控制zero-padding的数目。

    dilation(扩张)：控制kernel点（卷积核点）的间距; 也被称为 "à trous"算法. 可以在此github地址查看:Dilated convolution animations

    groups(卷积核个数)：这个比较好理解，通常来说，卷积个数唯一，但是对某些情况，可以设置范围在1 —— in_channels中数目的卷积核：

    输出[b,out_channels,w,h],其中w和h是输出的shape.

    
```
    mylayer=torch.nn.Conv2d(3,2,kernel_size=3,stride=2,padding=0)
    print(l1.weight.shape)
    input=torch.rand(1,3,7,7)
    out=l1.forward(input)
    print(out.shape)
-----------------------------------------------
    torch.Size([2, 3, 3, 3])
    torch.Size([1, 2, 3, 3])
```

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/cnn1.gif](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/cnn1.gif)