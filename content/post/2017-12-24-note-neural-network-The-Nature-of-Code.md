---
title: 笔记-神经网络-代码本色
author: Heaven Zone
date: '2017-12-24'
slug: note-neural-network-The-Nature-of-Code
categories:
  - 神经网络
tags:
  - 笔记
  - 神经网络
  - processing
description: '代码本色--用编程模拟自然系统'
---

## 简介

最近看了一本书，中文名是《代码本色--用编程模拟自然系统》，英文名是《The Nature of Code》，作者是纽约大学Tisch艺术学院艺术教授Daniel Shiffman，可以访问[作者网站](http://shiffman.net)获取更多有趣的信息。

该书用processing语言（作者也用其他语言做了相同的事情，可以看作者github）模拟自然系统，例如模拟加速度、地球引力、粒子系统等等，看起来也蛮有意思。

该书也介绍了人工神经网络，下面主要是对神经网络部分介绍做一下笔记。

## 人工神经网络导论


神经网络的学习策略：

- 监督式学习
  - 导师式
  - 训练集，测试集
- 非监督式学习
  - 不需要训练
- 增强式学习


应用场景：

- 模式识别
- 时序预测
- 信号处理
- 控制
- 软传感器
- 异常检测

## 感知器

感知器由一个或多个输入、一个处理器和单个输出构成。

感知器：[输入0, 输入1, ...] => 处理器 => 输出

### 步骤

- 接受输入
  - 输入0：x1 =12
  - 输入1：x2 =4
- 输入加权
  - 权重0：0.5
  - 权重1：-1
  - 每个输入乘以加权
  - 输入0 × 权重0 = 12 × 0.5 = 6
  - 输入1 × 权重1 = 4 × -1 = -4
- 输入求和
  - 总和 = 6 + (-4) = 2
- 产生输出
  - 将_总和_传入一个激励函数（activation function）后，我们就可以得到感知器的输出。
  - 输出可以是一个简单的二进制数。激励函数也可以很复杂。
  - 本例的激励函数：总和为正数，输出为1；若为复数，输出为-1
  - 输出 = sign(总和) => sign(2) => +1
  

### 感知器算法

- 对每个输入，将它乘以对应的权重；
- 对加权后的输入求和；
- 把总和传入一个激励函数（返回符号），得到感知器的输出。

### 感知器processing代码

把**输入**和**权重**分别存放到两个数组中，

```
float[] inputs = {12, 4};
float[] weights = {0.5, -1};
```

上面的**对每个输入**这句话暗示了一个循环，每一轮循环都将输入乘以权重，并在循环中求和：

```
float num = 0;
for (int i = 0; i < inputs.length; i++) {
  sum += inputs[i] * weights[i];
}
```

得到总和传入激励函数，然后计算得到结果：

```
float output = activate(sum);

int activate(float num) {
  if (sum > 0) return 1;
  else return -1;
}
```

## 用感知器进行简单的模式识别

平面有很多点和一条直线，识别点我们用神经网络判断某一点位于直线哪一边。

还要考虑点（0，0），无论权重是多少，总和总是0，无办法判断该点位于直线上方还是下方。

为避免这个问题，我们还要有第三个输入，通常称为**偏置输入**
。

（我们让）偏置输入总是等于1，也有相应的权重。

回到点（0，0），它的输入如下：

0 × x的权重 = 0
0 × y的权重 = 0
1 × 偏置的权重 = 偏置的权重

如果偏置的权重是正数，该点位于直线上方，如果为负数，则位于下方。

## 实现感知器

```
# 我们构建感知器类，感知器只需要存放输入权重：
class Perceptron {
  float[] weights;

  # 构造函数接受了一个参数，指定输入的数量，数组的长度也等于此参数
  Perceptron ( int n ) {
    weights = new float[n];
    for (int i = 0; i < weights.length; i++) {
      weights[i] = random[-1, 1]
    }
  }
  
  # 感知器能接收输入数据，并产生输出，我们把这些需求封装成一个
  feedforward()函数
  int feedforward( float[] inputs ) {
    float sum = 0;
    for ( int i = 0; i < weights.length; i++ ) {
      sum += inputs[i] * weights[i];
    }
    return activate(sum)
  }
}

```

我们现在就可以创建一个Perceptron（感知器）对象，然后传入一个点，让它猜测这个点处于直线哪一边。

```
Perceptron p = new Perceptron(3);
float[] point = {50, -12, 1};
int result = p.feedforward(point);
```

目前该感知器才对结果的概率只有50%，因为在创建感知器时，我们用随机数初始化每个权重（上面代码还没写）。神经网络并没有魔法，除非我们教他怎么做。

为了训练神经网络，让它能才处正确的结果，我们引入导论里面说的**监督式学习方法**。


学习过程：

1. 向感知器提供输入，这些输入的答案是已知的。
1. 让感知器猜测答案
1. 计算误差（感知器是否猜到了正确答案？）
1. 根据误差调整所有权重
1. 回到步骤（1）并重复

步骤1-4可以封装成一个函数，关于第3、4步，我们如何定义误差？如何根据误差调整权重？

误差的定义：

$$误差 = 正确输出 - 猜测输出$$

关于误差：

- 正确答案：猜测答案：误差
- -1：-1：0
- -1：+1：-2
- +1：-1：+2
- +1：+1：0

关于调整权重：

$$新权重 = 权重 + \Delta权重$$

而$\Delta权重$公式为：

$$\Delta权重 = 误差 \times 输入$$

因此：

$$新权重 = 权重 + 误差 \times 输入$$

为了可以控制调整权重的速度，我们可以增加一个**学习**常数：

$$新权重 = 权重 + 误差 \times 输入 \times 学习常数$$

关于**学习常数**：

- 学习常数越大，权重变化幅度越大，可以让我们更快解决问题。
- 但是权重变化幅度太大，可能会错过最优权重。
- 学习常数越小，权重变化越慢，学习过程所花时间越多。
- 权重调整幅度越小，能够提高神经网络的整体精度。


下面假设学习常数变量为c，我们按照上述方法实现感知器的**训练函数**：

```
# 学习常数c
float c = 0.01;

# 步骤1，提供输入和已知答案，它们都被传入train函数
void train(float[] inputs, int desired) {

  # 步骤2，根据输入作出猜测
  int guess = feedforward(inputs);
  
  # 步骤3，计算误差
  float error = desired - guess;
  
  # 步骤4，根据误差和学习常数调整权重
  for (int i = 0; i < weights.length; i++) {
    weights[i] = weight[i] + c * error * inputs[i];
  }
}
```

感知器类的完整代码如下：

```
class Perceptron {
  float[] weights;
  float c = 0.01;
  
  Perceptron(int n) {
    weights = new float[n];
    for (int i = 0; i < weights.length; i++){
      # 以随机数初始化权重
      weights[i] = random(-1, 1)
    }
  }
  
  # 根据输入返回输出
  int feedforward(float[] inputs) {
    float sum = 0;
    for(int i = 0; i < weights.length; i++){
      sum = sum + inputs[i] * weights[i];
    }
    return activate(sum);
  }
  
  # 判断输出是1还是-1
  int activate(float sum) {
    if(sum > 0) return 1;
    else return -1;
  }
  
  # y用已知数据训练神经网络
  void train(float[] inputs, int desired) {
    int guess = feedforward(inputs);
    float error = desired - gess;
    for (int i = 0; i < weights.length; i++) {
      weights[i] = weights[i] + c * error * inputs[i];
    }
  }
}
```


为了训练感知器，我们需要一系列已知答案的输入。我们可以将这部分操作封装成一个类：

```
class Trainer{
  float[] inputs;
  int answer;
  Trainer(float x, float y, int a) {
    inputs = new float[3];
    inputs[0] = x;
    inputs[1] = y;
    inputs[2] = 1;
    
    answer = a;
  }
}
```

我们如何判断点在直线上面？还是在下面？

假设我们有一条直线：

$$y=2 \times x + 1$$

计算x位置对应的y坐标的函数：

```
float f(float x) {
  return 2 * x + 1;
}
```

生成随机点：

```
float x = random(width);
float y = random(height);
```

计算随机点x坐标对应在直线上的y坐标：

```
float yline = f(x);
```


如果点位于直线上方，那么点的y坐标将小于yline（y坐标往下面方向递增）。

```
if (y < yline) {
  answer = -1;
} else {
  answer = 1;
}
```

有了这些，我们可以用输入和正确答案创建一个Trainer对象:

```
Trainer t = new Trainer(x, y, answer);
```

假设我们有一个感知器对象ptron，我们只需向它的train()函数传入输入和对应答案，就能训练这个感知器。

```
ptron.train(t.inputs, t.answer)
```


完整代码可以到作者的github查看[[传送门](https://github.com/shiffman/The-Nature-of-Code-Examples/tree/master/chp10_nn/NOC_10_01_SimplePerceptron)]。



## 参考资料

- Daniel Shiffman著. 周晗彬译.《代码本色》《the nature of code》.人民邮电出版社
- <https://github.com/shiffman/The-Nature-of-Code-Examples/tree/master/chp10_nn/NOC_10_01_SimplePerceptron>