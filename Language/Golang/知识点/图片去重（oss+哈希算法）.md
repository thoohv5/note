# 图片去重（oss+哈希算法）

### 哈希算法

- **pHash（Perceptual Hash）** —— 感知哈希，能识别“看起来一样”的图片。
- **aHash（Average Hash）** —— 平均哈希，简单快速。
- **dHash（Difference Hash）** —— 差值哈希，检测结构变化。

### pHash（Perceptual Hash）

pHash中文叫感知哈希算法，通过离散余弦变换(DCT)降低图片频率，相比aHash有更好鲁棒性。

基本原理：

1. **缩小尺寸**。将图片缩小为32*32大小。
2. **灰度化处理**。
3. **计算DCT，并选取左上角8*8的矩阵。**DCT是一种特殊的傅立叶变换，将图片从像素域变换为频率域，并且DCT矩阵从左上角到右下角代表越来越高频率的系数，但是除左上角外，其他地方的系数为0或接近0，因此只保留左上角的低频区域。
4. **计算DCT均值**。
5. **哈希值计算**。将每个DCT值，与平均值进行比较。大于或等于平均值，记为1，小于平均值，记为0，由此生成二进制数组。*（与aHash类似）*
6. **图片配对，计算汉明距离**

# 代码

```python
# -*- coding: utf-8 -*-
import pandas as pd
import cv2
import time
import numpy as np

def pHash(img,leng=32,wid=32):
    img = cv2.resize(img, (leng, wid))   
    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    dct = cv2.dct(np.float32(gray))
    dct_roi = dct[0:8, 0:8]            
    avreage = np.mean(dct_roi)
    phash_01 = (dct_roi>avreage)+0
    phash_list = phash_01.reshape(1,-1)[0].tolist()
    hash = ''.join([str(x) for x in phash_list])
    return hash

def dHash(img,leng=9,wid=8):
    img=cv2.resize(img,(leng, wid))
    image=cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)
    #每行前一个像素大于后一个像素为1，相反为0，生成哈希
    hash=[]
    for i in range(wid):
        for j in range(wid):
            if image[i,j]>image[i,j+1]:
                hash.append(1)
            else:
                hash.append(0)
    return hash

def aHash(img,leng=8,wid=8):
    img=cv2.resize(img,(leng, wid))
    image=cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)
    avreage = np.mean(image)                           
    hash = [] 
    for i in range(image.shape[0]): 
        for j in range(image.shape[1]): 
            if image[i,j] >= avreage: 
                hash.append(1) 
            else: 
                hash.append(0) 
    return hash

def Hamming_distance(hash1,hash2):
    num = 0
    for index in range(len(hash1)):
        if hash1[index] != hash2[index]:
            num += 1
    return num 

if __name__ == '__main__':
    
    image1 = cv2.imread('image1')
    image2 = cv2.imread('image2')
    
    start1 = time.time()
    d_dist = Hamming_distance(dHash(image1),dHash(image2))
    end1 = time.time()
    
    start2 = time.time()
    p_dist = Hamming_distance(pHash(image1),pHash(image2))
    end2 = time.time()
    
    start3 = time.time()
    a_dist = Hamming_distance(aHash(image1),aHash(image2))
    end3 = time.time()
    
    print('a_dist is '+'%d' % a_dist + ', similarity is ' +'%f' % (1 - a_dist * 1.0 / 64) + ', time is ' +'%f' % (end3-start3))
    print('p_dist is '+'%d' % p_dist + ', similarity is ' +'%f' % (1 - p_dist * 1.0 / 64) + ', time is ' +'%f' % (end2-start2))
    print('d_dist is '+'%d' % d_dist + ', similarity is ' +'%f' % (1 - d_dist * 1.0 / 64) + ', time is ' +'%f' % (end1-start1))
```