
---
title: TensorFlow-6.TensorBoard
date: 2020-02-18 23:40:54
tags: TensorFlow
category:
        - 机器学习和深度学习
        - TensorFlow
---
TensorFlow 使用 TensorBoard 来提供计算图形的图形图像。这使得理解、调试和优化复杂的神经网络程序变得很方便。TensorBoard 也可以提供有关网络执行的量化指标。它读取 TensorFlow 事件文件，其中包含运行 TensorFlow 会话期间生成的摘要数据。

## 一、步骤
    
    确定想要的 OP 摘要
    loss=tf...

    tf.summary.scalar('loss',loss)

    tf.summary.histogram 可视化梯度、权重或特定层的输出分布

