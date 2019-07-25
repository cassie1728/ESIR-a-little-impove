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

　　采用K阶多项式拟合水平中线，以图片中心做原点，标准化坐标。多项式中有`K+1`个参数。
![](https://github.com/cassie1728/ESIR-a-little-impove/raw/master/esir4.jpg)
<br>
　　L条线段由下方公式表示，其中![](http://chart.googleapis.com/chart?cht=tx&chl=$$r_l$$)表示线段长度。有`3L`个参数。
![](https://github.com/cassie1728/ESIR-a-little-impove/raw/master/esir5.jpg)
<br>
　　所以需要学习的参数一种有`3L+K+1`个。这些参数一旦确定，L条线段的`2L`个端点就可以计算，这里2L个端点就相当于ASTER中的K个控制点。将2L个端点标准化到(-0.5,0.5)之间，再做TPS变换。其余步骤与ASTER相同。

#### 2. Iterative Rectification

![](https://github.com/cassie1728/ESIR-a-little-impove/raw/master/esir3.jpg)

　　ASTER只能生成一张矫正图，ESIR则在这点做了改进，通过迭代可以生成多张矫正图。（反正最后也只用一张…不知道为啥非得这么说 == ）<br>

　　通过多次矫正，即可提升矫正效果。<br>

　　但是如果不加控制的多次直接迭代，则会造成“边界效应（boundary effect）”问题，即每次迭代都会使一部分像素点在采样区域外面，这样就会忽视掉一些text的像素点。另外，由于多次进行双线性插值，也会对图像清晰度造成影响。
<br>
<br>
　　针对边界效应和清晰度影响，ESIR设计了如图的迭代流程。![](http://chart.googleapis.com/chart?cht=tx&chl=$$T$$)是TPS的参数矩阵，![](http://chart.googleapis.com/chart?cht=tx&chl=$$P_l$$),![](http://chart.googleapis.com/chart?cht=tx&chl=$$P_2$$)…是经过变换后得到的参数（2L个端点坐标）。这里的操作就是将中间产生的矫正图仅仅用作估计参数![](http://chart.googleapis.com/chart?cht=tx&chl=$$P_i$$)，然后使用原始的Input Image计算参数矩阵![](http://chart.googleapis.com/chart?cht=tx&chl=$$T$$)，再进行矫正，这样就可以有效解决以上两个问题。

#### 3. parameter initialization

　　这类模型训练时的主要问题就是，矫正网络对参数初始化十分敏感。ASTER和RARE中也提到，完全随机的初始化参数会导致收敛问题，其产生的高度扭曲的图片会影响识别网络的效果，进而影响矫正网络（因为矫正网络的训练是由识别效果做导向的）。
<br>
　　ESIR首先定义了一个辅助参数![](http://chart.googleapis.com/chart?cht=tx&chl=$$P_0$$)，开始等于![](http://chart.googleapis.com/chart?cht=tx&chl=$$P'$$)，计算![](http://chart.googleapis.com/chart?cht=tx&chl=$$P=P_0+\DeltaP$$)
