---
title: 深度学习(逻辑回归模型)
categories: 阅读
date: 2024-10-11 10:20:45
tags: 人工智能

---

# 深度学习(逻辑回归模型)

**逻辑回归（Logistic Regression）**是一种广泛使用的统计方法，主要用于解决分类问题。尽管名字中含有“回归”，但逻辑回归实际上是一种分类算法，用于预测二分类或多分类问题。

逻辑回归模型的核心是通过**线性组合**将特征值转化为一个实数，然后使用 **sigmoid 函数**把这个实数**转化**为一个在 0 和 1 之间的**概率值**。这样，我们才能做出分类决策

## 模型

**sigmoid function** 激活函数

或者也被称为

**logistic function** 逻辑函数

逻辑回归中，sigmoid 函数是一种用于将模型的输出值转换为概率值的数学函数。它的形式为：
$$
z = w \cdot x + b
$$

$$
g(z) = \frac{1}{1 + e^{-z}}
$$

$$
0 < g(z) < 1
$$

sigmoid 函数的特点包括：

1. **取值范围**：函数输出的值介于 0 和 1 之间。这使得它非常适合用于表示概率。
2. **S 形曲线**：sigmoid 函数的图形呈现 S 形，当输入值为 0 时，输出为 0.5；输入值趋于正无穷大时，输出接近 1；输入值趋于负无穷大时，输出接近 0。
3. **单调性**：函数是单调递增的，意味着随着输入值的增加，输出值也会不断增加。

在逻辑回归中，我们使用 sigmoid 函数将线性组合的输入（即特征的加权和）转换为二分类问题的概率值。这使得模型能够输出预测某个类的概率，从而进行分类决策。

## 决策边界

当
$$
z = w \cdot x + b = 0
$$
时，这条线被称为决策边界

根据多项式的不同，决策边界的图形也会有非常大的不同，可能会是一个很复杂的图形

### 1. 数据分类

在二分类问题中，逻辑回归的目标是根据输入特征将数据分为两个类别（通常标记为 0 和 1）。决策边界定义了模型如何进行这种分类。具体来说：

- 当一个实例的输入特征 �*x* 位于决策边界的一侧（例如，当 �>0*z*>0 时），模型会预测这个实例属于类别 1。
- 当实例位于决策边界的另一侧（例如，当 �<0*z*<0 时），模型会预测它属于类别 0。

### 2. 概率阈值

由于逻辑回归输出的是一个概率值（通过 sigmoid 函数转换），决策边界实际上对应于一个概率阈值。通常情况下，使用 0.5 作为阈值进行分类：

- 如果模型输出的概率大于 0.5，输出类别 1。
- 如果模型输出的概率小于 0.5，输出类别 0。

### 3. 可视化

在二维特征空间中，决策边界可以用图形表示出来。通过绘制决策边界，可以直观地看到模型如何将不同类别的数据点分割开来。

### 4. 模型性能

决策边界的形状和位置会直接影响模型的分类性能。一个合适的决策边界可以更好地拟合训练数据，并在测试数据上保持良好的泛化能**力。**



**注意：下面所有的f(x)都是g(z)**

## 损失函数（loss）

当 y = 1时
$$
L(f_{w,b}(x^{(i)}),y^{(i)}) = - log(f_{w,b}(x^{(i)}))
$$
当y = 0 时
$$
L(f_{w,b}(x^{(i)}),y^{(i)}) = - log(1 - f_{w,b}(x^{(i)}))
$$

### 简化

$$
L(f_{w,b}(x^{(i)}),y^{(i)}) = - y^{(i)}log(f_{w,b}(x^{(i)})) - (1 - y^{(i)})log(1 - f_{w,b}(x^{(i)})
$$



## 成本函数

$$
J(w,b) = - \frac 1 m \sum_{i = 1}^{m} [y^{(i)}log(f_{w,b}(x^{(i)})) + (1 - y^{(i)})log(1 - f_{w,b}(x^{(i)}))]
$$

###  最大似然

最大似然估计（Maximum Likelihood Estimation, MLE）是一种统计方法，它用于估计模型参数，使得观测到的数据在给定模型下的可能性（即似然）最大化



## 梯度下降

$$
w_j = w_j - \alpha [\frac 1 m \sum_{i=1}^{m}(f_{w,b}(x^{(i)}) -y^{(i)})x_j^{(i)}]
$$

$$
b = b - \alpha [\frac 1 m \sum_{i=1}^{m}(f_{w,b}(x^{(i)}) -y^{(i)})]
$$



## 过拟合问题

### 什么是欠拟合？

欠拟合是指模型无法捕捉数据中的基本趋势或结构，导致其在训练数据和新数据上的表现都很差。这通常发生在模型过于简单，无法表达数据中的复杂性或规律。

### 欠拟合的表现

- **训练集表现差**：模型在训练集上的准确率或损失很高，表明它不能很好地拟合训练数据。
- **测试集表现差**：模型在未见过的数据上的表现也很差，通常与训练集的表现相似。

### 原因

- **模型过于简单**：如线性回归用于处理非线性问题，或使用了太少的特征。
- **特征选择不当**：没有包含信息丰富的特征，或者使用了错误的特征。
- **不足的训练**：模型训练不充分，迭代次数太少，导致未能有效学习训练数据。

### 如何检测欠拟合？

- 通过绘制训练误差和验证误差曲线。如果两个曲线都很高，且彼此接近，通常表明模型存在欠拟合。

### 解决欠拟合的方法

1. **增加模型复杂度**：使用更复杂的模型，比如增加多项式的次数，或使用更多的特征。
2. **特征工程**：增加更多的信息特征，可能还需要进行特征转换。
3. **增加训练时间**：如果模型简单但训练时间不足，增加迭代次数可能有助于提升性能。

### 什么是过拟合？

过拟合（Overfitting）是指一个模型在训练数据上表现得非常好，但在新数据（测试数据）上的表现却很差。这种情况通常发生在模型过于复杂，学习了训练数据中的噪声和随机波动，而不仅仅是数据的实际趋势或规律。

### 过拟合的表现

- **训练集表现好**：模型在训练集上的准确率或损失很低。
- **测试集表现差**：一旦在未见过的数据上测试，准确率或损失急剧下降。

### 原因

- **复杂模型**：如使用了多项式回归的高次项，或者深度神经网络层数过多。
- **样本量不足**：数据量少时，模型容易记住训练数据的细节。
- **噪声过多**：如果训练数据包含很多噪声，模型可能学习到这些噪声。

### 如何检测过拟合？

- 使用验证集来监测模型在训练过程中的表现。若训练误差持续下降而验证误差开始上升，则可能发生过拟合。

### 解决办法

1、扩大数据集

2、筛选特征

3、**正则化**

## 正则化

### 什么是正则化？

正则化（Regularization）是一种技术，通过增加对模型复杂度的惩罚，来控制模型的复杂性，从而减少过拟合。正则化旨在提高模型在未知数据上的表现。

### 常见的正则化方法

1. L1 正则化（Lasso）

   - 通过在损失函数中添加权重绝对值的和（L1范数）作为惩罚项：

   $$
   J(w) = \text{原始损失} + \frac \lambda {2m} \sum_{j=1}^{n} |w_j|
   $$

   

- 它可以迫使某些权重变为零，从而进行特征选择。

1. L2 正则化（Ridge）

   - 通过在损失函数中添加权重平方的和（L2范数）作为惩罚项：

   $$
   J(w) = \text{原始损失} + \frac \lambda {2m} \sum_{j=1}^{n} w_j^2
   $$

   

- 它会抑制权重的大小，使模型的复杂度降低，但不会使权重为零。

1. Dropout
   - 在训练过程中随机丢弃一部分神经元，使模型在每次迭代时只能依赖部分输入。
   - 这种方式迫使神经网络学习更为鲁棒的特征。

### 正则化的效果

- **简单模型**：通过加入对复杂度的惩罚，正则化可以使模型变得更简单，从而更好地泛化到新数据。
- **平衡**：目标是在训练集和验证集上都取得良好表现，避免训练集的学习过度。

### 小结

- **过拟合**是指模型在训练数据上表现良好但在新数据上表现差的现象，这通常与模型复杂度过高有关。
- **正则化**是一种防止过拟合的技术，通过对模型复杂度施加惩罚，促进模型的泛化能力。常见的方法包括 L1 和 L2 正则化以及 Dropout。

### 正则化后的函数

$$
w_j = w_j - \alpha [\frac 1 m \sum_{i=1}^{m}(f_{w,b}(x^{(i)}) -y^{(i)})x_j^{(i)}] + \frac \lambda m w_j
$$

b不更新



# 实践

通过学历、笔试成绩、项目经验、实习经验 判断 面试通过率
$$
f(x) = wx_1*x_1 + wx_2 + wx_3*x_4
$$
create_model.py

```
import pandas as pd  
import numpy as np  
import torch  
import torch.nn as nn  
import torch.optim as optim  
from sklearn.model_selection import train_test_split  
from sklearn.preprocessing import StandardScaler  

# 读取数据  

data = pd.read_csv('data.csv')  

# 特征与目标变量  

X = data[['学历', '笔试水平', '项目经验', '实习经验']]  
y = data['面试结果'].values  

# 生成多项式特征  

X['学历^2'] = X['学历'] ** 2  
X['项目经验*实习经验'] = X['项目经验'] * X['实习经验']  

# 特征标准化  

scaler = StandardScaler()  
X_scaled = scaler.fit_transform(X)  

# 划分训练集与测试集  

X_train, X_test, y_train, y_test = train_test_split(X_scaled, y, test_size=0.2, random_state=42)  

# 转换为PyTorch张量  

X_train_tensor = torch.FloatTensor(X_train)  
y_train_tensor = torch.FloatTensor(y_train)  
X_test_tensor = torch.FloatTensor(X_test)  

# 构建逻辑回归模型  

class LogisticRegressionModel(nn.Module):  
    def __init__(self, input_dim):  
        super(LogisticRegressionModel, self).__init__()  
        self.linear = nn.Linear(input_dim, 1)  # 输出为1个值  

    def forward(self, x):  
        return torch.sigmoid(self.linear(x))  # Sigmoid激活函数  

# 初始化模型、损失函数和优化器  

input_dim = X_train.shape[1]  
model = LogisticRegressionModel(input_dim)  
criterion = nn.BCELoss()  # 二元交叉熵损失  
optimizer = optim.Adam(model.parameters(), lr=0.01)  

# 训练模型  

num_epochs = 100  
for epoch in range(num_epochs):  
    model.train()  
    

    # 前向传播  

    outputs = model(X_train_tensor).squeeze()  # 去掉多余的维度  
    loss = criterion(outputs, y_train_tensor)  

    # 正则化

    # loss = criterion(outputs, y_train_tensor) + lambda_reg * torch.norm(model.linear.weight, 2)

    # 后向传播和优化  (梯度下降)

    optimizer.zero_grad()   # 清除之前的梯度  
    loss.backward()  # 计算当前梯度  
    optimizer.step()  # 更新参数  

    if (epoch+1) % 10 == 0:  
        print(f'Epoch [{epoch+1}/{num_epochs}], Loss: {loss.item():.4f}')  

# 模型评估  

model.eval()  
with torch.no_grad():  
    y_pred = model(X_test_tensor).squeeze()  
    y_pred = (y_pred >= 0.5).float()  # 使用0.5作为阈值  

# 输出测试结果  

accuracy = (y_pred.numpy() == y_test).mean()  
print(f'Accuracy: {accuracy:.2%}')  

# 步骤 4: 保存为ONNX格式  

dummy_input = torch.FloatTensor(X_test_tensor[:1])  # 选择一条样本作为输入  
onnx_file_path = 'model.onnx'  
torch.onnx.export(model, dummy_input, onnx_file_path,   
                  input_names=['input'], output_names=['output'],  
                  dynamic_axes={'input': {0: 'batch_size'}, 'output': {0: 'batch_size'}})  
print(f'Model exported to {onnx_file_path}')
```



testonnx.cpp

```
#include <iostream>  
#include <string>
#include <opencv2/opencv.hpp>  
#include <opencv2/dnn/dnn.hpp>  

using std::endl;
using std::cout;
using cv::Mat;
using std::string;

int main() {  
    // 加载 ONNX 模型  
    string modelFile = "model.onnx"; // 替换为你的 ONNX 模型文件路径  
    cv::dnn::Net net = cv::dnn::readNetFromONNX(modelFile);  

    if (net.empty()) {  
            std::cerr << "Failed to load model from " << modelFile << std::endl;  
            return -1;  
    } 

    // 准备输入数据（根据实际模型的输入）  
    // 这里假设模型需要的输入为 6 个特征  
    Mat inputBlob =cv::Mat(1, 6, CV_32F); // 1 样本，6 特征  
    
    float x1 ,x2 ,x3 ,x4;
    x1 = 3.0;
    x2 = 85.0;
    x3 = 73.0;
    x4 = 65.0;

    inputBlob.at<float>(0, 0) = x1; // 学历  
    inputBlob.at<float>(0, 1) = x2; // 笔试水平  
    inputBlob.at<float>(0, 2) = x3; // 项目经验  
    inputBlob.at<float>(0, 3) = x4; // 实习经验  
    inputBlob.at<float>(0, 4) = x1 * x1; 
    inputBlob.at<float>(0, 5) = x3 * x4; 

    // 进行推理  
    net.setInput(inputBlob);  
    Mat outputBlob = net.forward();  

    // 输出结果  
    cout << "Predicted output: " << outputBlob.at<float>(0, 0) << endl;  

    return 0;  
}  
```



data.csv

```
学历,笔试水平,项目经验,实习经验,面试结果
3,85.87,75.2,77.99,1
5,92.01,100.0,76.0,1
4,90.92,76.03,81.05,1
4,70.12,100.0,74.22,1
2,77.8,79.39,71.7,0
2,83.57,57.14,79.93,0
2,94.78,53.94,93.79,0
5,89.82,92.24,66.43,1
4,81.92,76.65,100.0,1
4,84.98,90.71,50.72,1
1,84.15,72.1,62.72,0
5,98.29,83.91,93.82,1
5,89.7,72.3,89.21,1
3,90.13,52.28,65.66,1
2,80.97,63.3,66.88,0
2,89.69,82.85,62.6,0
.......
```

