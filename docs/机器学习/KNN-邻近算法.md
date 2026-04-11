# KNN-邻近算法

K最近邻(K-Nearest Neighbor, KNN)算法是一种基本分类与回归方法，该方法的思想非常简单：如果一个样本在特征空间中的k个最相似(即特征空间中最邻近)的样本中的大多数属于某一个类别，则该样本也属于这个类别。

## 算法原理

KNN算法的工作原理可以概括为以下步骤：

1. 计算测试样本与训练集中每个样本的距离
2. 按距离递增排序
3. 选取与当前样本距离最小的k个样本
4. 统计这k个样本中每个类别的频数
5. 返回频数最高的类别作为测试样本的预测类别

## 距离计算方法

在KNN算法中,常用的距离计算方法有：

- **欧氏距离**（最常用）：$d(x, y) = \sqrt{\sum_{i=1}^{n}(x_i - y_i)^2}$
- **曼哈顿距离**：$d(x, y) = \sum_{i=1}^{n}|x_i - y_i|$
- **闵可夫斯基距离**：$d(x, y) = (\sum_{i=1}^{n}|x_i - y_i|^p)^{\frac{1}{p}}$

## 代码实现

### 使用scikit-learn库

```python
from sklearn.neighbors import KNeighborsClassifier
from sklearn.model_selection import train_test_split
from sklearn.datasets import load_iris
from sklearn.metrics import accuracy_score

# 加载鸢尾花数据集
iris = load_iris()
X = iris.data
y = iris.target

# 划分训练集和测试集
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42)

# 创建KNN分类器，k=3
knn = KNeighborsClassifier(n_neighbors=3)

# 训练模型
knn.fit(X_train, y_train)

# 预测
y_pred = knn.predict(X_test)

# 评估准确率
accuracy = accuracy_score(y_test, y_pred)
print(f"准确率: {accuracy:.2f}")
```

## K值的选择

K值的选择对KNN算法的性能有重要影响：

- **K值过小**：模型复杂度高，容易过拟合，对噪声敏感
- **K值过大**：模型过于简单，可能欠拟合

通常使用交叉验证来选择最优的K值。

## 优缺点

### 优点

- 算法简单，易于理解和实现
- 无需训练过程
- 对异常值不敏感
- 适合多分类问题

### 缺点

- 计算量大，特别是样本数量多时
- 需要大量内存存储训练数据
- 对不平衡数据敏感
- 需要进行特征缩放

## 应用场景

- 图像识别
- 推荐系统
- 文本分类
- 异常检测
