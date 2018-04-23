---
title: OpenCV-2-读取视频流
date: 2018-04-23 15:59:25
tags:
 - OpenCV
 - python
categories:
 - OpenCV
 - python
---


# OpenCV-2-读取视频流

视频[链接](https://www.dropbox.com/s/7mbo35zrxd1aefz/peopleCounter.avi?dl=0)


## 读取视频文件
```python
import numpy as np
import cv2

cap = cv2.VideoCapture('peopleCounter.avi') #Open video file

while(cap.isOpened()):
    ret, frame = cap.read() #read a frame
    try:
        cv2.imshow('Frame',frame)
    except:
        #if there are no more frames to show...
        print('EOF')
        break

    #Abort and exit with 'Q' or ESC
    k = cv2.waitKey(30) & 0xff
    if k == 27:
        break

cap.release() #release video file
cv2.destroyAllWindows() #close all openCV windows
```

然后我们用VideoCapture对象打开视频文件，将视频文件的位置作为参数。

我们从视频中读取相框并逐一展示，直到我们到达结尾。 此时，我们退出while循环并关闭视频文件和视频窗口。
使用网络摄像头非常相似.
唯一的区别是我们如何创建VideoCapture对象。 这次我们将它作为参数传递给它。 这表示我们要使用ID为0的网络摄像头。您的计算机上是否有多个网络摄像头，例如USB网络摄像头，另一个嵌入了您的屏幕上，您需要传递0或1，具体取决于您想要哪一个使用。

VideoCapture对象有几个[属性](http://docs.opencv.org/3.1.0/d8/dfe/classcv_1_1VideoCapture.html#aeb1644641842e6b104f244f049648f94)，您可以访问并且有时会更改.

## 从摄像头读取视频

```python
import numpy as np
import cv2

cap = cv2.VideoCapture(0)
    
while(cap.isOpened()):
    ret, frame = cap.read()
    try:
        cv2.imshow('Frame',frame)
    except:
        print('EOF')
        break

    k = cv2.waitKey(30) & 0xff
    if k == 27:
        break

cap.release()
cv2.destroyAllWindows()
```

下面是一个示例，显示所有属性的值并更改网络摄像头中视频流的宽度和高度.

```python
import numpy as np
import cv2

cap = cv2.VideoCapture(0)

#show all video properties
for i in range(19):
    print(i, cap.get(i))

cap.set(3,160) #set width
cap.set(4,120) #set height
    
while(cap.isOpened()):
    ret, frame = cap.read()
    try:
        cv2.imshow('Frame',frame)
    except:
        print('EOF')
        break

    k = cv2.waitKey(30) & 0xff
    if k == 27:
        break

cap.release()
cv2.destroyAllWindows()



```

