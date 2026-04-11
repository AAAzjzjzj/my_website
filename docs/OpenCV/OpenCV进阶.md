# OpenCV进阶

本文介绍 OpenCV 的进阶应用，包括图像滤波、边缘检测、特征检测等。

## 图像滤波

### 均值滤波

```python
import cv2
import numpy as np

# 均值滤波
blur = cv2.blur(img, (5, 5))
```

### 高斯滤波

```python
# 高斯滤波
gaussian = cv2.GaussianBlur(img, (5, 5), 0)
```

### 中值滤波

```python
# 中值滤波（对椒盐噪声效果好）
median = cv2.medianBlur(img, 5)
```

### 双边滤波

```python
# 双边滤波（保边去噪）
bilateral = cv2.bilateralFilter(img, 9, 75, 75)
```

## 形态学操作

### 腐蚀与膨胀

```python
# 定义结构元素
kernel = np.ones((5, 5), np.uint8)

# 腐蚀
erosion = cv2.erode(img, kernel, iterations=1)

# 膨胀
dilation = cv2.dilate(img, kernel, iterations=1)
```

### 开运算与闭运算

```python
# 开运算（先腐蚀后膨胀，去除小物体）
opening = cv2.morphologyEx(img, cv2.MORPH_OPEN, kernel)

# 闭运算（先膨胀后腐蚀，填充小孔）
closing = cv2.morphologyEx(img, cv2.MORPH_CLOSE, kernel)
```

### 形态学梯度

```python
# 形态学梯度（膨胀图与腐蚀图之差）
gradient = cv2.morphologyEx(img, cv2.MORPH_GRADIENT, kernel)
```

## 边缘检测

### Canny边缘检测

```python
# Canny边缘检测
edges = cv2.Canny(img, 100, 200)
```

### Sobel算子

```python
# Sobel算子
sobelx = cv2.Sobel(img, cv2.CV_64F, 1, 0, ksize=5)  # x方向
sobely = cv2.Sobel(img, cv2.CV_64F, 0, 1, ksize=5)  # y方向
```

### Laplacian算子

```python
# Laplacian算子
laplacian = cv2.Laplacian(img, cv2.CV_64F)
```

## 轮廓检测

```python
# 转换为灰度图
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

# 二值化
ret, thresh = cv2.threshold(gray, 127, 255, cv2.THRESH_BINARY)

# 查找轮廓
contours, hierarchy = cv2.findContours(thresh, cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)

# 绘制轮廓
cv2.drawContours(img, contours, -1, (0, 255, 0), 2)

# 轮廓特征
for cnt in contours:
    # 面积
    area = cv2.contourArea(cnt)
    
    # 周长
    perimeter = cv2.arcLength(cnt, True)
    
    # 外接矩形
    x, y, w, h = cv2.boundingRect(cnt)
    cv2.rectangle(img, (x, y), (x+w, y+h), (0, 255, 0), 2)
    
    # 最小外接圆
    (x, y), radius = cv2.minEnclosingCircle(cnt)
    center = (int(x), int(y))
    radius = int(radius)
    cv2.circle(img, center, radius, (0, 255, 0), 2)
```

## 图像阈值处理

### 简单阈值

```python
# 二值阈值
ret, thresh1 = cv2.threshold(gray, 127, 255, cv2.THRESH_BINARY)

# 反二值阈值
ret, thresh2 = cv2.threshold(gray, 127, 255, cv2.THRESH_BINARY_INV)

# 截断阈值
ret, thresh3 = cv2.threshold(gray, 127, 255, cv2.THRESH_TRUNC)

# 阈值化为0
ret, thresh4 = cv2.threshold(gray, 127, 255, cv2.THRESH_TOZERO)
```

### 自适应阈值

```python
# 自适应均值阈值
adaptive_mean = cv2.adaptiveThreshold(gray, 255, 
                                      cv2.ADAPTIVE_THRESH_MEAN_C,
                                      cv2.THRESH_BINARY, 11, 2)

# 自适应高斯阈值
adaptive_gaussian = cv2.adaptiveThreshold(gray, 255,
                                          cv2.ADAPTIVE_THRESH_GAUSSIAN_C,
                                          cv2.THRESH_BINARY, 11, 2)
```

### Otsu阈值

```python
# Otsu自动阈值
ret, otsu = cv2.threshold(gray, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)
```

## 直方图

### 计算直方图

```python
# 灰度直方图
hist = cv2.calcHist([gray], [0], None, [256], [0, 256])

# 彩色图像直方图
color = ('b', 'g', 'r')
for i, col in enumerate(color):
    hist = cv2.calcHist([img], [i], None, [256], [0, 256])
```

### 直方图均衡化

```python
# 灰度图直方图均衡化
equ = cv2.equalizeHist(gray)

# 彩色图直方图均衡化（对每个通道）
img_yuv = cv2.cvtColor(img, cv2.COLOR_BGR2YUV)
img_yuv[:, :, 0] = cv2.equalizeHist(img_yuv[:, :, 0])
img_output = cv2.cvtColor(img_yuv, cv2.COLOR_YUV2BGR)
```

## 模板匹配

```python
# 读取模板
template = cv2.imread('template.jpg', 0)
w, h = template.shape[::-1]

# 模板匹配
res = cv2.matchTemplate(gray, template, cv2.TM_CCOEFF_NORMED)

# 设置阈值
threshold = 0.8
loc = np.where(res >= threshold)

# 绘制矩形
for pt in zip(*loc[::-1]):
    cv2.rectangle(img, pt, (pt[0] + w, pt[1] + h), (0, 255, 0), 2)
```

## 霍夫变换

### 霍夫直线检测

```python
# 边缘检测
edges = cv2.Canny(gray, 50, 150)

# 霍夫直线检测
lines = cv2.HoughLines(edges, 1, np.pi/180, 200)

# 绘制直线
for line in lines:
    rho, theta = line[0]
    a = np.cos(theta)
    b = np.sin(theta)
    x0 = a * rho
    y0 = b * rho
    x1 = int(x0 + 1000 * (-b))
    y1 = int(y0 + 1000 * (a))
    x2 = int(x0 - 1000 * (-b))
    y2 = int(y0 - 1000 * (a))
    cv2.line(img, (x1, y1), (x2, y2), (0, 0, 255), 2)
```

### 霍夫圆检测

```python
# 霍夫圆检测
circles = cv2.HoughCircles(gray, cv2.HOUGH_GRADIENT, 1, 20,
                          param1=50, param2=30, minRadius=0, maxRadius=0)

# 绘制圆
if circles is not None:
    circles = np.uint16(np.around(circles))
    for i in circles[0, :]:
        cv2.circle(img, (i[0], i[1]), i[2], (0, 255, 0), 2)
        cv2.circle(img, (i[0], i[1]), 2, (0, 0, 255), 3)
```

## 应用场景

- 图像增强和去噪
- 目标检测和分割
- 形状识别
- 文档扫描
- 车道线检测
- 圆形物体检测
