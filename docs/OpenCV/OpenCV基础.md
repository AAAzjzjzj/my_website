# OpenCV基础

OpenCV (Open Source Computer Vision Library) 是一个开源的计算机视觉和机器学习软件库。

## 安装

```bash
pip install opencv-python
pip install opencv-contrib-python  # 包含额外模块
```

## 基本操作

### 读取图像

```python
import cv2
import numpy as np

# 读取图像
img = cv2.imread('image.jpg')

# 读取灰度图像
img_gray = cv2.imread('image.jpg', cv2.IMREAD_GRAYSCALE)

# 显示图像
cv2.imshow('Image', img)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

### 保存图像

```python
# 保存图像
cv2.imwrite('output.jpg', img)
```

### 图像属性

```python
# 获取图像尺寸
height, width, channels = img.shape
print(f"高度: {height}, 宽度: {width}, 通道数: {channels}")

# 获取图像数据类型
print(f"数据类型: {img.dtype}")

# 获取图像大小（像素总数）
print(f"图像大小: {img.size}")
```

## 颜色空间转换

```python
# BGR转RGB
img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)

# BGR转灰度
img_gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

# BGR转HSV
img_hsv = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)
```

## 图像基本操作

### 访问和修改像素

```python
# 访问像素值
pixel = img[100, 100]  # 返回 [B, G, R]
blue = img[100, 100, 0]

# 修改像素值
img[100, 100] = [255, 255, 255]  # 设置为白色
```

### 图像ROI（感兴趣区域）

```python
# 提取ROI
roi = img[100:200, 150:250]

# 修改ROI
img[100:200, 150:250] = [0, 255, 0]  # 设置为绿色
```

### 图像通道分离与合并

```python
# 分离通道
b, g, r = cv2.split(img)

# 合并通道
img_merged = cv2.merge([b, g, r])

# 单独处理某个通道
img[:, :, 2] = 0  # 将红色通道设为0
```

## 图像绘制

### 绘制线条

```python
# 绘制直线
cv2.line(img, (0, 0), (200, 200), (255, 0, 0), 2)
```

### 绘制矩形

```python
# 绘制矩形
cv2.rectangle(img, (50, 50), (200, 200), (0, 255, 0), 2)
```

### 绘制圆形

```python
# 绘制圆形
cv2.circle(img, (100, 100), 50, (0, 0, 255), -1)  # -1表示填充
```

### 添加文字

```python
# 添加文字
font = cv2.FONT_HERSHEY_SIMPLEX
cv2.putText(img, 'OpenCV', (50, 50), font, 1, (255, 255, 255), 2)
```

## 视频处理

### 读取视频

```python
# 打开视频文件
cap = cv2.VideoCapture('video.mp4')

# 或打开摄像头
cap = cv2.VideoCapture(0)

while cap.isOpened():
    ret, frame = cap.read()
    if not ret:
        break
    
    # 处理帧
    cv2.imshow('Frame', frame)
    
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
```

### 保存视频

```python
# 定义编码器和创建VideoWriter对象
fourcc = cv2.VideoWriter_fourcc(*'XVID')
out = cv2.VideoWriter('output.avi', fourcc, 20.0, (640, 480))

while cap.isOpened():
    ret, frame = cap.read()
    if ret:
        out.write(frame)
    else:
        break

cap.release()
out.release()
```

## 常用函数

### 图像缩放

```python
# 指定尺寸缩放
resized = cv2.resize(img, (300, 300))

# 按比例缩放
resized = cv2.resize(img, None, fx=0.5, fy=0.5)
```

### 图像旋转

```python
# 获取旋转矩阵
rows, cols = img.shape[:2]
M = cv2.getRotationMatrix2D((cols/2, rows/2), 45, 1)

# 应用旋转
rotated = cv2.warpAffine(img, M, (cols, rows))
```

### 图像翻转

```python
# 水平翻转
flipped_h = cv2.flip(img, 1)

# 垂直翻转
flipped_v = cv2.flip(img, 0)

# 水平垂直翻转
flipped_both = cv2.flip(img, -1)
```

## 应用场景

- 图像处理和分析
- 目标检测和识别
- 人脸识别
- 视频分析
- 增强现实
- 机器人视觉
