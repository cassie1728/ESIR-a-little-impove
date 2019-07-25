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

　　与ASTER使用K个控制点辅助矫正不同，ESIR使用1条水平中线(拟合文字走向)和L条线段（估计竖直方向和边界）来辅助矫正。<br>

　　采用多项式拟合水平中线，以图片中心做原点，标准化坐标。多项式中有`K+1`个参数。
![](https://github.com/cassie1728/ESIR-a-little-impove/raw/master/esir4.jpg)
<br>
　　L条线段由下方公式表示，其中![](http://chart.googleapis.com/chart?cht=tx&chl=$$r_l$$)表示线段长度。有`3L`个参数。
![](https://github.com/cassie1728/ESIR-a-little-impove/raw/master/esir5.jpg)
<br>
　　所以需要学习的参数一种有`3L+K+1`个。这些参数一旦确定，L条线段的`2L`个端点就可以计算，这里2L个端点就相当于ASTER中的K个控制点。将2L个端点标准化到(-0.5,0.5)之间，再做TPS变换。其余步骤与ASTER相同。

#### 2. Iterative Rectification


