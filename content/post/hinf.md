+++
title = "H无穷控制系统设计的一般流程"
date = 2018-04-08
draft = false
summary = "最简单的H无穷控制的方式采用双端子模型进行设计，这篇文档利用仿真验证的那篇文章中的算法作为例子"

tags = ["Control Theory"]
categories = ["Intelligent Transportation"]

[header]
image = ""
caption = ""
preview = true

+++

>最简单的H无穷控制的方式采用双端子模型进行设计，这篇文档利用正在进行仿真验证的那篇文章中的算法作为例子。

#### 设计大纲

* 获得关于控制系统的基本框图以及各传递函数
* 将系统的框图表达为双端子模型的形式
* 求解双端子模型的广义系统的传递函数
* 利用传递函数构建matlab兼容的控制模型
* 构建反馈控制系统求解控制器K的取值
* 利用K的取值计算实际的控制器模型并且仿真验证

#### 控制系统的基本框图
![](http://osv1xytac.bkt.clouddn.com/17-9-25/53649515.jpg)
在这里，将D与We均设定为1，也就是直接传递。其余的传递函数表现形式如下所示：
$$
G(s) = \frac{e^{-0.02s}}{s^2(0.1s+1)}
$$
$$
H(s) = s+1
$$
K是一个双输入单输出的PI控制器模型。
#### 转换为双端子模型的形式
双端子模型的具体形式：
![](http://osv1xytac.bkt.clouddn.com/17-9-25/93257588.jpg)
就是将整个系统中除去K的部分单独提取出来作为一个广义的控制系统，K的输入是$u_{i-1}(s)$,$e_i(s)$,输出是$\epsilon_i(s)$

广义系统P的输入就是$u_{i-1}(s)$,$e_i(s)$，输出是$e_i(s)$,$u_i(s)$,$u_{i-1}(s)$,$e_i(s)$，前两者是整个系统整体的输出，后两者是给控制器K的输入。

$$w = u_{i-1}(s)$$
$$z = u_i(s),e_i(s)$$

$$v = e_ i(s),u_ {i-1}(s)$$

$$r = \epsilon_i(s)$$

#### 双端子模型的广义传递函数
P是一个双输入四输出的控制系统，它的传递函数矩阵如下所示：
$$\left[
 \begin{matrix}
   e_ i(s)  \\
   u_ i(s) \\
   e_ i(s)  \\
  u_ {i-1}(s)
  \end{matrix}
  \right] = \left[
 \begin{matrix}
   G(s) & -G(s)  \\
   0 & H^{-1}(s) \\
   G(s) & -G(s)  \\
   1 & 0
  \end{matrix}
  \right]  \left[
 \begin{matrix}
   u_ {i-1}(s)  \\
   \epsilon_ i(s) \\
  \end{matrix}
  \right]$$

#### 构建matlab兼容的控制模型
使用hinfsyn函数，函数的具体介绍如下图所示：

>hinfsyn computes a stabilizing H∞ optimal lti/ss controller K for a partitioned lti plant P.
P = [A B1 B2;C1 D11 D12;C2 D21 D22]
The controller, K, stabilizes the P and has the same number of states as P. The system P is partitioned where inputs to B1 are the disturbances, inputs to B2 are the control inputs, output of C1 are the errors to be kept small, and outputs of C2 are the output measurements provided to the controller. B2 has column size (NCON) and C2 has row size (NMEAS). The optional KEY and VALUE inputs determine tolerance, solution method and so forth.
The closed-loop system is returned in CL. This closed-loop system is given by CL = lft(P,K) as in the following diagram.

代码块如下图所示：

```python
% Thansfer multiple transfer functions into sate space models
clc 
clear all;
% Step1：输入传递函数分子分母系数
%From u1 to y1
num1=[0 0 1 ; 0 0 0];
den1=[0.1 1 0 0];
%[A,B,C,D] = tf2ss(num1,den1);
%From u2 to y1
num2=[0 0 -1 -1; 0.1 1 0 0 ];
den2=[0.1 1.1 1 0 0];
%[A,B,C,D] = tf2ss(num2,den2)

%From u1 to y2
num3=[0 0 0 1 ;0.1 1 0 0];
den3=[0.1 1 0 0];
%From u2 to y2
num4=[0 0 -1 ; 0 0 0];
den4=[0.1 1 0 0];
% Step2：获得各单输入单输出状态空间模型
[A11,B11,C11,D11]=tf2ss(num1,den1);%From u1 to y1
[A21,B21,C21,D21]=tf2ss(num2,den2);%From u2 to y1
[A12,B12,C12,D12]=tf2ss(num3,den3);%From u1 to y2
[A22,B22,C22,D22]=tf2ss(num4,den4);%From u2 to y2
S_m11=ss(A11,B11,C11,D11);%From u1 to y1
S_m21=ss(A21,B21,C21,D21);%From u2 to y1
S_m12=ss(A12,B12,C12,D12);%From u1 to y2
S_m22=ss(A22,B22,C22,D22);%From u2 to y2
% Step3：按输入输出将状态空间模型写成矩阵形式，即为双输入双输出状态空间模型
P=[S_m11 S_m12;S_m21 S_m22];
% Step3：获得A，B，C，D矩阵
[A,B,C,D] = ssdata(P);
[K,CL,GAM] = hinfsyn(P,2,1);
[nenx,denx] = ss2tf(K.a,K.b,K.c,K.d,1);
[neny,deny] = ss2tf(K.a,K.b,K.c,K.d,2);
```