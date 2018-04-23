---
title: OpenCV-1-图片处理
date: 2018-04-23 15:58:56
tags:
 - OpenCV
 - python
 - 图像处理
categories:
 - OpenCV
 - python
---


# OpenCV-1-图片处理

下载图片![pic](OpenCV-1-图片处理/Lenna.png)
```python
import cv2
import numpy as np

img = cv2.imread('Lenna.png') #load RGB image

imgGS = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY) #convert RGB to grayscale
cv2.imwrite("LennaGS.png",imgGS) #save grayscale image

ret,imgBW = cv2.threshold(imgGS,127,255,cv2.THRESH_BINARY) #binarization
cv2.imwrite("LennaBW.png",imgBW) #save binary image

cv2.imshow('Original image', img) #Display original image
cv2.imshow('Grayscale image', imgGS) #Display grayscale image
cv2.imshow('Binary image', imgBW) #Display binarized image
cv2.waitKey(0) #exit with 'Q' or ESC
cv2.destroyAllWindows()

```

![pic](OpenCV-1-图片处理/Lenna.png)
![pic](OpenCV-1-图片处理/LennaGS.png)
![pic](OpenCV-1-图片处理/LennaBW.png)

