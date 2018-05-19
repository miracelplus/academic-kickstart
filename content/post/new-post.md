+++
title = "蒙特卡洛马尔科夫链"
date = 2018-05-08T13:17:26+08:00
draft = false
summary = "了解蒙特卡洛马尔科夫链(MCMC)，以及在重要性采样方面的应用"

tags = ["MCMC","Sampling"]
categories = ["statics"]

[header]
image = ""
caption = ""
preview = true

+++

>这篇笔记主要参考的是[随机采样方法整理与讲解](https://www.cnblogs.com/xbinworld/p/4266146.html)这篇文章。

#### 蒙特卡洛数值积分
采用$\int_a^b \frac{f(x)}{q(x)} q(x)dx$来近似的计算[a,b]内的f(x)的数值积分，在q(x)下抽取n个样本，在n足够大的时候，就可以采用均值近似积分取值。
接下来就是如何对于一个概率分布取得数值样本的方法，也就是抽样方法。

#### 蒙特卡洛规则
X为随机变量，服从概率分布P(x)，要想计算f(X)的期望，只要不停的从p(x)中抽取$x_i$，然后对这些$f(x_i)$取平均值即可得到$f(x)$的期望。
$$E_N(f)=\frac{1}{N} \sum_i^Nf(x^{(i)}) $$

#### 接收-拒绝采样
在$p(x)$难以直接采样的时候，我们可以设定一个程序可抽样的分布$q(x)$例如高斯分布，然后拒绝某些样本，就可以得到类似$p(X)$采样的分布的目的。
![拒绝采样例图](http://osv1xytac.bkt.clouddn.com/18-5-14/2138900.jpg)
具体操作：
设计一个q(x)例如高斯分布，以及一个常量k，使得p(X)总在kq(x)的下方。

* x轴：从q(x)分布抽样得到a
* y轴：从均匀分布(0,kq(a))中抽样得到u
* 如果在灰色区域u>p(a)，那么拒绝掉这次抽样，否则接收这次抽样。

>问题：高维下合适的q分布不好找，很难设计合理的k值

#### 重要性抽样：

公式：
$$E[f] = \int f(z)p(z)dz = \int f(z)\frac{p(z)}{q(z)}dz = \frac{1}{L} \sum_{i=1}^{L} \frac{p(z^{(l)}}{q(z^{(l)}}f(z^{(l)})$$

p,f是确定的，我们需要确定一个q，我们的目的是让p*f/q的方差越小越好，所以p*f打的地方，q(z)也应该越大

#### 马尔科夫链，马尔科夫稳态

没有后效性的状态就是马尔科夫链，体现的是状态空间中的转换关系，在特定的情况下，无论从哪个起点开始，最终状态空间都会收敛到一个特定的解。

马尔科夫链的两个性质：

* 周期性：有限次状态转移回到自身
* 不可约：两个状态之间相互转移

一个没有周期性且不可约的马尔科夫过程：各态遍历的


**马氏链定理**：如果一个非周期的马氏链具有转移概率矩阵P，而且它的任何两个状态都是联通的，那么$\lim_ {n\to \infty} P_ {ij}^n$存在且和i无关，我们记$\lim_ {n\to \infty} P_ {ij}^n = \pi (j)$，我们有：
![](http://osv1xytac.bkt.clouddn.com/18-5-15/87820375.jpg)

>对于给定的概率分布p(x),我们希望能有便捷的方式生成它对应的样本。由于马氏链能收敛到平稳分布， 于是一个很的漂亮想法是：如果我们能构造一个转移矩阵为P的马氏链，使得该马氏链的平稳分布恰好是p(x), 那么我们从任何一个初始状态$x_0$出发沿着马氏链转移, 得到一个转移序列 $x_0$,$x_1$,...$x_n$, 如果马氏链在第n步已经收敛了，于是我们就得到了 π(x) 的样本$x_n$⋯。

#### MCMC(马尔科夫蒙特卡洛)方法

**马尔科夫链的细致平稳性：**
在非周期的马尔科夫链额度概率转移矩阵和每一个状态的概率满足：
$$\pi p(j|i) = \pi (j)p(i|j)$$
最终得到的状态$\pi$就是该马尔科夫链的平稳分布。

**MCMC算法具体流程：**

* 随机选取样本取值作为接下来的备选点
* 计算当前样本概率与备选点概率
* 若备选点概率大于当前样本概率：更新当前样本并加入样本库
* 若备选点概率低于当前样本概率：投出随机数
* 若两概率之比高于随机数：接收样本并更新
* 否则放弃该样本
* 重新回到第一步，寻找新的样本取值


**matlab代码实现：**

```python
xlen = 200000;
x = zeros(2,xlen);
x0 = [60,-10] ;
len = length(x);
k = 1 ;
table = csvread('value_table_new2.csv');
new_table = zeros(size(table));
km = [];
while k <= len
    nextx = [98*rand(),-20*rand()];
    %compute the p from x0
    [pn,nxnum,nynum] = get_possibility(nextx,table);
    %km = [km,pn];
    [p0,zxnum,zynum] = get_possibility(x0,table);
    p = min(pn/p0,1);
    if p >= 1
        x(1,k) = nextx(1);
        x(2,k) = nextx(2);
        x0 = nextx ;
        k = k + 1 ; 
        new_table(zxnum,zynum) = new_table(zxnum,zynum)+1;
    else
        pp = rand();
        if pp < p
            x(1,k) = nextx(1);
            x(2,k) = nextx(2);
            x0 = nextx   ;
            k = k + 1 ; 
            new_table(zxnum,zynum) = new_table(zxnum,zynum)+1;
        end
        
    end
    
end
hist = histc(abs(x(1,1:k-1)),0:0.4:100);
plot(hist);   
figure;
imagesc(new_table);
colorbar;
```