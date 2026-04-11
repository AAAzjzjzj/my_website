# 逻辑回归与Sigmoid函数

逻辑回归是一种广义线性回归模型，主要用于二分类问题。尽管名字中有"回归"，但它实际上是一种分类算法。

## Sigmoid函数

Sigmoid函数（也称为逻辑函数）是逻辑回归的核心，其数学表达式为：

$$\sigma(z) = \frac{1}{1 + e^{-z}}$$

### Sigmoid函数的特点

- 输出值在 (0, 1) 之间
- 在 z=0 处，函数值为 0.5
- 函数是单调递增的
- 具有良好的导数性质

导数公式：

$$
\sigma'(z) = \sigma(z)(1-\sigma(z))
$$

## 逻辑回归模型

逻辑回归模型的假设函数为：

$$h_\theta(x) = \sigma(\theta^Tx) = \frac{1}{1 + e^{-\theta^Tx}}$$

其中：
- θ 是模型参数
- x 是输入特征
- h_θ(x) 表示样本属于正类的概率

## 代价函数

逻辑回归使用对数损失（交叉熵）作为代价函数：

$$J(\theta) = -\frac{1}{m}\sum_{i=1}^{m}[y^{(i)}\log(h_\theta(x^{(i)})) + (1-y^{(i)})\log(1-h_\theta(x^{(i)}))]$$

## 梯度下降

通过梯度下降法更新参数：

$$\theta_j := \theta_j - \alpha\frac{\partial J(\theta)}{\partial\theta_j}$$

其中梯度为：

$$\frac{\partial J(\theta)}{\partial\theta_j} = \frac{1}{m}\sum_{i=1}^{m}(h_\theta(x^{(i)}) - y^{(i)})x_j^{(i)}$$

## 代码实现

```python
from sklearn.linear_model import LogisticRegression
from sklearn.datasets import load_breast_cancer
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score, classification_report
import numpy as np
import matplotlib.pyplot as plt

# 加载数据集
data = load_breast_cancer()
X = data.data
y = data.target

# 划分训练集和测试集
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42)

# 创建逻辑回归模型
lr = LogisticRegression(max_iter=10000)

# 训练模型
lr.fit(X_train, y_train)

# 预测
y_pred = lr.predict(X_test)
y_prob = lr.predict_proba(X_test)

# 评估
accuracy = accuracy_score(y_test, y_pred)
print(f"准确率: {accuracy:.2f}")
print("\n分类报告:")
print(classification_report(y_test, y_pred))

# 可视化Sigmoid函数
z = np.linspace(-10, 10, 100)
sigmoid = 1 / (1 + np.exp(-z))

plt.figure(figsize=(10, 6))
plt.plot(z, sigmoid)
plt.xlabel('z')
plt.ylabel('σ(z)')
plt.title('Sigmoid函数')
plt.grid(True)
plt.axhline(y=0.5, color='r', linestyle='--', label='决策边界')
plt.axvline(x=0, color='r', linestyle='--')
plt.legend()
plt.show()
```

## 多分类问题

逻辑回归可以扩展到多分类问题：

### One-vs-Rest (OvR)

为每个类别训练一个二分类器，预测时选择概率最大的类别。

### Softmax回归

使用Softmax函数将输出转换为概率分布：

$$P(y=k|x) = \frac{e^{\theta_k^Tx}}{\sum_{j=1}^{K}e^{\theta_j^Tx}}$$

## 正则化

为了防止过拟合，可以添加正则化项：

### L1正则化（Lasso）

$$J(\theta) = -\frac{1}{m}\sum_{i=1}^{m}[...] + \lambda\sum_{j=1}^{n}|\theta_j|$$

### L2正则化（Ridge）

$$J(\theta) = -\frac{1}{m}\sum_{i=1}^{m}[...] + \lambda\sum_{j=1}^{n}\theta_j^2$$

## 优缺点

### 优点

- 实现简单，计算效率高
- 输出具有概率意义
- 可以处理大规模数据
- 易于理解和解释

### 缺点

- 只能处理线性可分问题
- 对特征工程要求较高
- 容易欠拟合
- 对异常值敏感

## 应用场景

- 垃圾邮件分类
- 信用卡欺诈检测
- 疾病诊断
- 点击率预测
- 客户流失预测
