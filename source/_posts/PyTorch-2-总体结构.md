---
title: PyTorch-2-总体结构
date: 2020-04-14 15:16:47
tags: PyTorch
categories:
        - 机器学习和深度学习
        - PyTorch
---

https://pytorch-cn.readthedocs.io/zh/latest/

PyTorch文档中主要包含2个模块，一个是 torch, 一个 torchvision, torch 是主模块, 用来搭建神经网络的, torchvision 是辅模块, 有数据库, 还有一些已经训练好的神经网络等着你直接用, 比如 (VGG, AlexNet, ResNet).

    torch:
        Tensor
        Storage
        nn
        nn.functional
        autograd
        optim
        nn.init
        multiprocessing
        legacy
        cuda
        utils.fit
        utils.data
        utils.model_zoo

    torchvison:
        datasets
        models
        transforms
        utils


