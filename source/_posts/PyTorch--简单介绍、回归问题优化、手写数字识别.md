---
title: PyTorch--简单介绍、回归问题优化、手写数字识别
date: 2018-01-18 18:02:23
tags: [pytorch]
categories:
		- 深度学习
		- PyTorch
---
>本文首发于我的博客：gongyanli.com  
>学习教程：[莫烦的PyTorch视频](https://morvanzhou.github.io/tutorials/machine-learning/torch/)


前言：简单介绍了一下PyTorch，然后根据自己学的东西跑了以下两个代码，有时间的时候我会把代码注释加上，希望能够对更多的朋友有用。我学习PyTorch是在莫烦Python上学习的，良心推荐。
## PyTorch是什么？
	
	PyTorch是一个深度学习框架，和TensorFlow、Caffe等一样。PyTorch的前身是Torch，但Torch使用
	Lua语言写的，而Lua语言本身不流行，所以推广度不高，但是Torch真的是很好用。所以就用python对Torch进行重写，于是就有了今天的PyTorch。
## PyTorch优势
	
	PyTorch能够在强大的GPU加速基础上实现张量和动态神经网络；
	动态图计算特性：目前市场上支持动态图计算的有PyTorch、DyNet和Chainer，而支持静态图计算的有TensorFlow、MXNet、Theano；
	我们不能单纯的说支持动态计算就绝对的好，但是在很多情况下非常方便有效，所以要具体情况具体分析；
	虽然TensorFlow本身不支持动态计算，但它也能通过某种特定的手段来支持动态计算；
	PyTorch能够和numpy自由转换；
	PyTorch由Facebook开发，与google的TensorFlow相比也拥有很强的实力。
## 回归问题优化
    `import torch
	import torch.utils.data as Data
	import torch.nn.functional as F
	from torch.autograd import Variable
	import matplotlib.pyplot as plt
	
	# hyper parameters
	LR = 0.01
	BATCH_SIZE = 32
	EPOCH = 12
	
	x = torch.unsqueeze(torch.linspace(-1, 1, 1000), dim=1)
	y = x.pow(2) + 0.1 * torch.normal(torch.zeros(*x.size()))
	
	# plot dataset
	# plt.scatter(x.numpy(),y.numpy())
	# plt.show()
	
	torch_dataset = Data.TensorDataset(data_tensor=x, target_tensor=y)
	loader = Data.DataLoader(dataset=torch_dataset, batch_size=BATCH_SIZE, shuffle=True, num_workers=2)
	
	
	# default network
	class Net(torch.nn.Module):
	    def __init__(self):
	        super(Net, self).__init__()
	        self.hidden = torch.nn.Linear(1, 20)  # hidden layer
	        self.predict = torch.nn.Linear(20, 1)  # output layer
	
	    def forward(self, x):
	        x = F.relu(self.hidden(x))  # activation function for hidden layer
	        x = self.predict(x)  # linear output
        return x

	# different nets
	net_SGD=Net()
	net_Momentum=Net()
	net_RMSprop=Net()
	net_Adam=Net()
	nets=[net_SGD,net_Momentum,net_RMSprop,net_Adam]
	
	opt_SGD=torch.optim.SGD(net_SGD.parameters(),lr=LR)
	opt_Momentum=torch.optim.SGD(net_Momentum.parameters(),lr=LR,momentum=0.8)
	opt_RMSprop=torch.optim.RMSprop(net_RMSprop.parameters(),lr=LR,alpha=0.9)
	opt_Adam=torch.optim.Adam(net_Adam.parameters(),lr=LR,betas=(0.9,0.99))
	optimizer = [opt_SGD,opt_Momentum,opt_RMSprop,opt_Adam]
	
	loss_func=torch.nn.MSELoss()
	losses_his=[[],[],[],[]]  # record loss
	
	for epoch in range(EPOCH):
	    print(epoch)
	    for step,(batch_x,batch_y) in enumerate(loader):
	        b_x=Variable(batch_x)
	        b_y=Variable(batch_y)
	
	        for net,opt,l_his in zip(nets,optimizer,losses_his):
	            output=net(b_x)
	            loss=loss_func(output,b_y)
	            opt.zero_grad()
	            loss.backward()
	            opt.step()
	            l_his.append(loss.data[0])
	
	labels = ['SGD', 'Momentum', 'RMSprop', 'Adam']
	for i, l_his in enumerate(losses_his):
	    plt.plot(l_his, label=labels[i])
	plt.legend(loc='best')
	plt.xlabel('Steps')
	plt.ylabel('Loss')
	plt.ylim((0, 0.2))
	plt.show()

`
结果如图：
![](http://p2lakvkq0.bkt.clouddn.com/pytorch2.jpg)
## 手写数字识别
    `import torch
	import torch.nn as nn
	from torch.autograd import Variable
	import torch.utils.data as Data
	import torchvision
	import matplotlib.pyplot as plt
	
	# Hyper Parameters
	EPOCH = 1
	BATCH_SIZE = 50
	LR = 0.001
	DOWNLOAD_MNIST = False
	
	train_data = torchvision.datasets.MNIST(
	    root='./mnist',
	    train=True,
	    transform=torchvision.transforms.ToTensor(),  # (0,1) (0-255)
	    download=DOWNLOAD_MNIST
	)

	# plot one example
	# print(train_data.train_data.size())                 # (60000, 28, 28)
	# print(train_data.train_labels.size())               # (60000)
	# plt.imshow(train_data.train_data[0].numpy(), cmap='gray')
	# plt.title('%i' % train_data.train_labels[0])
	# plt.show()
	
	train_loader = Data.DataLoader(dataset=train_data, batch_size=BATCH_SIZE, shuffle=True, num_workers=2)
	
	test_data = torchvision.datasets.MNIST(root='./mnist', train=False)
	test_x = Variable(torch.unsqueeze(test_data.test_data, dim=1), volatile=True).type(torch.FloatTensor)[:2000] / 255
	test_y = test_data.test_labels[:2000]


	class CNN(nn.Module):

    def __init__(self):
        super(CNN, self).__init__()
        self.conv1 = nn.Sequential(
            nn.Conv2d(  # (1,28,28)
                in_channels=1,
                out_channels=16,
                kernel_size=5,
                stride=1,
                padding=2,  # if stride=1,padding=(kernel_size-1)/2=(5-1)/2=2
            ),  # ->(16,28,28)
            nn.ReLU(),  # ->(16,28,28)
            nn.MaxPool2d(kernel_size=2),  # ->(16,14,14)
        )
        self.conv2 = nn.Sequential(  # (16,14,14)
            nn.Conv2d(16, 32, 5, 1, 2),  # ->(32,14,14)
            nn.ReLU(),  # ->(32,14,14)
            nn.MaxPool2d(2)  # ->(32,7,7)
        )
        self.out = nn.Linear(32 * 7 * 7, 10)

    def forward(self, x):
        x = self.conv1(x)
        x = self.conv2(x)  # (batch,32,7,7)
        x = x.view(x.size(0), -1)  # (batch,32*7*7)
        output = self.out(x)
        return output


	cnn = CNN()
	# print(cnn)


	optimizer = torch.optim.Adam(cnn.parameters(), lr=LR)
	loss_func = nn.CrossEntropyLoss()
	
	for epoch in range(EPOCH):
	    print(epoch)
	    for step, (x, y) in enumerate(train_loader):
	        b_x = Variable(x)
	        b_y = Variable(y)
	
	        output = cnn(b_x)
	        loss = loss_func(output, b_y)
	        optimizer.zero_grad()
	        loss.backward()
	        optimizer.step()
	
	        if step % 50 == 0:
	            test_output = cnn(test_x)
	            pred_y = torch.max(test_output, 1)[1].data.squeeze()
	            accuracy = sum(pred_y == test_y) / test_y.size(0)
	            print('Epoch: ', epoch, '| train loss: %.4f' % loss.data[0], '| test accuracy: %.2f' % accuracy)
	
	# print 10 predictions from test data
	test_output= cnn(test_x[:10])
	pred_y = torch.max(test_output, 1)[1].data.numpy().squeeze()
	print(pred_y, 'prediction number')
	print(test_y[:10].numpy(), 'real number')
`
结果如图：
![](http://p2lakvkq0.bkt.clouddn.com/pytorch1.jpg)