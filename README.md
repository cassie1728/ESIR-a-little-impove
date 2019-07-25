# ESIR-a-little-impove
paper share : 2019 ESIR : End-to-end Scene Text Recognition via Iterative Image Rectification

Based on ASTER,ESIR have some improvements.

### 贡献点（3个）

1. a novel line-fitting transformation. 使用直线和线段帮助矫正形变
2. iterative rectification framework. 多次（迭代）矫正
3. parameter initialization and superior recognition performance. 参数初始化创新+识别率稍有提高

### Pipeline and Struction

![](https://github.com/cassie1728/ESIR-a-little-impove/raw/master/esir1.jpg)

ESIR的大致流程和ASTER基本一致，改进地方主要在矫正网络部分：改变了拟合形变的方式，并增加了迭代的矫正流程。

#### 1. line-fitting transformation

![](https://github.com/cassie1728/ESIR-a-little-impove/raw/master/esir2.jpg)

