---
title: TensorFlow-1.介绍
date: 2020-02-18 21:24:09
tags: TensorFlow
---
## 一、介绍
开源深度学习库 TensorFlow 允许将深度神经网络的计算部署到任意数量的 CPU 或 GPU 的服务器、PC 或移动设备上，且只利用一个 TensorFlow API。你可能会问，还有很多其他的深度学习库，如 Torch、Theano、Caffe 和 MxNet，那 TensorFlow 与其他深度学习库的区别在哪里呢？包括 TensorFlow 在内的大多数深度学习库能够自动求导、开源、支持多种 CPU/GPU、拥有预训练模型，并支持常用的NN架构，如递归神经网络（RNN）、卷积神经网络（CNN）和深度置信网络（DBN）。


TensorFlow 则还有更多的特点，如下：
    支持所有流行语言，如 Python、C++、Java、R和Go。
    可以在多种平台上工作，甚至是移动平台和分布式平台。
    它受到所有云服务（AWS、Google和Azure）的支持。
    Keras——高级神经网络 API，已经与 TensorFlow 整合。
    与 Torch/Theano 比较，TensorFlow 拥有更好的计算图表可视化。
    允许模型部署到工业生产中，并且容易使用。
    有非常好的社区支持。
    TensorFlow 不仅仅是一个软件库，它是一套包括 TensorFlow，TensorBoard 和 TensorServing 的软件。

谷歌 research 博客列出了全球一些使用 TensorFlow 开发的有趣项目：

    Google 翻译运用了 TensorFlow 和 TPU（Tensor Processing Units）。
    Project Magenta 能够使用强化学习模型生成音乐，运用了 TensorFlow。
    澳大利亚海洋生物学家使用了 TensorFlow 来发现和理解濒临灭绝的海牛。
    一位日本农民运用 TensorFlow 开发了一个应用程序，使用大小和形状等物理特性对黄瓜进行分类。
## 二、安装
    
    $ conda --version

    CPU 与 GPU 的对比：中央处理器（CPU）由对顺序串行处理优化的内核（4～8个）组成。图形处理器（GPU）具有大规模并行架构，由数千个更小且更有效的核芯（大致以千计）组成，能够同时处理多个任务。

    $ conda create -n tensorflow python=3.5 # 虚拟环境
    $ source activate tensorflow # 激活
    $ source deactivate 
    $ pip install tensorflow

    $ conda install -c anaconda ipython # 安装ipython
    $ conda install -channel=conda-forge nb_conda_kernels # 安装nb_conda_kernels
    $ jupyter notebook # 启动

    $ conda install dateutil

### 2.1 Linux下安装
    
    官网：https://www.anaconda.com/distribution/#macos
    参考：https://www.jianshu.com/p/742dc4d8f4c5

    # Linux 下安装anaconda
    $ wget https://repo.anaconda.com/archive/Anaconda3-2019.10-Linux-x86_64.sh
    $ sh Anaconda3-2019.10-Linux-x86_64.sh
    $ vi ~/.bashrc
      Anaconda3-2019.10-Linux-x86_64.sh


    $ conda create -n tensorflow python=3.7
    $ source activate tensorflow # 激活
    $ source deactivate   # conda deactivate

### 2.2 Linux安装jupyter notebook
    # 本地访问Linux服务器上的jupyter notebook
    $ conda install nb_conda

    1. 生成配置文件
    $ jupyter notebook --generate-config
    生成了.jupyter/jupyter_notebook_config.py配置文件.

    2. 打开python,生成密钥:

```
>>>from notebook.auth import passwd
 
>>>passwd()
 
#输入新的登陆密码（本地访问的时候使用）
 
#再次确认
 
#生成key，复制留存并修改到配置文件中去
```
    3. 修改配置文件
    $ vim  ~/.jupyter/jupyter_notebook_config.py

```
c.NotebookApp.ip='*'                     # 就是设置所有ip地址皆可访问
 
c.NotebookApp.password = u'sha:ce...     # 复制上步的那个密文key替换等号后面的内容，注意，有u'
 
c.NotebookApp.open_browser = False       # 禁止自动打开浏览器
 
c.NotebookApp.port =8888                 # 指定一个可用端口,也可以是其他闲置的端口
```

    4. 在服务器终端中执行jupyter notebook 并放入后台中
    $ nohup jupyter notebook &

### 2.3 Linux安装TensorFlow、cuda等
    #安装指定 版本的TensorFlow
    pip install tensorflow-gpu==1.9.0

    # 安装的包
    conda install cudatoolkit=9.0 -c https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/linux-64/
    conda install cudnn=7.1.2 -c https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/linux-64/


    1.检查CUDA版本：
    使用：
    nvcc -V
    或：
    cat /usr/local/cuda/version.txt

    2.检查cuDNN版本：
    cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2

    3.检查TensorFlow版本：
    直接使用pip list查看