---
layout: post
title: 平方根倒数速算法
date:  2018-08-13
categories: blog
tags: [组成原理, 奇技淫巧]
description: 
---

计算平方根倒数有许多应用，比如在3D游戏中求单位向量.在实时性要求比较高的场景中，计算速度比计算精度重要的多，因此一般不用标准的浮点函数库.

## 源代码里的神秘数字0x5f3759df
著名游戏《雷神之锤III》的代码在2002年左右被披露，人们发现了一段用于快速计算平方根倒数的代码.
```c
float FastInvSqrt(float x) {
  float xhalf = 0.5f * x;
  int i = *(int*)&x;          // get bits for floating value
  i = 0x5f3759df - (i >> 1);  // gives initial guess
  x = *(float*)&i;            // convert bits back to float
  x = x*(1.5f-xhalf*x*x);   // Newton step, repeating increases accuracy
  return x;
}
```

有人在Visual C++.NET平台做过粗略的测试，发现以上代码的效率是简单地调用库函数 (float) (1.0/sqrt(x)) 的4倍，而最大相对误差只有0.00175228，从而证明了上述代码的实用性.代码中最有意思的是0x5f379df这个常量.它是怎么得到的？这段代码的原理又是什么？

## [IEEE 754-1985](https://en.wikipedia.org/wiki/IEEE_754-1985)

IEEE 754标准定义了浮点数的存储方式，

|Level|s|E|M|$L$|$B$|
|-|-|-|-|-|-|
|Single precision|bit 31|bits 30:23|bits 22:0|$2^{23}$|127|
|Double precision|bit 63|bits 62:52|bits 51:0|$2^{52}$|1023|

其中，s是符号位(1表示负号)；E是指数的编码，是一个无符号整数，值等于指数大小加上偏移量B；M表示尾数，可表示定点小数m=M/L，小数点在最左边，取值范围为[0,1).特别地，当所有位都是0的时候，表示浮点数+0.0.当E全为0，而M不全为0时，表示一个绝对值很小的数（非规约小数），此时，E是最小的指数.因此，浮点数的指数部分应该加上一个偏移量B.
{s,E,M}表示的浮点数的值$x$为

$$x=(-1)^{s}\left(1+\frac{M}{L}\right)^{E-B}$$

如果将{0,E,M}解释成整数$z$，则$z=EL+M$

## 牛顿迭代法

构造函数$f(y)=y^{-2}-x$，则$g(y)=y-\frac{f(y)}{f'(y)}$的不动点就是$f(y)$的零点，
因为$f(y)$在其零点的邻域内有连续的2阶导数，所以当迭代初值比较接近零点时，牛顿迭代法至少是2阶收敛的.迭代式为：

$$
y_{n+1}=y_n-\frac{y_n^{-2}-x}{-2y_n^{-3}}
=\frac{3}{2}y_n-\frac{1}{2}xy_n^3
=y_n(\frac{3}{2}-\frac{x}{2}y_n^2)
$$

那么，迭代的初始值该如何确定呢.

## 对数函数的线性近似表示

函数$\varphi(m)=\log_2(1+m)$在区间(0,1)上的平均变化率$k$为: 

$$k=\frac{\varphi(1)-\varphi(0)}{1-0}=\log_2 2 - \log_2 1=1$$

则过$(0,0)$和$(1,1)$的割线方程为$y=x$.

由中值定理可知在区间(0,1)存在一点$\xi$，满足$\varphi'(\xi)=\frac{\log_2 \mathrm{e}}{1+\xi}=1$，解得$\xi=\log_2(\mathrm{e}/2)$
则过$(\xi,\varphi(\xi))$的切线方程为$y=x-\xi+\varphi(\xi)\triangleq x+\zeta=x+0.0860713320559342$.

则线性近似函数的表达式为$y=x+\sigma$，$\sigma\in(0, \zeta)$.这里$\sigma$的取值范围还比较粗略，$\sigma$的取值最后再讨论.


## 迭代初值的估计

令$y=x^{-1/2}$，则$y^2=x^{-1}$，两边取对数可得：

$$2\log_2 y = -\log_2 x$$

$(1+M_x/L)^{E_x-B}$，$(1+M_yL)^{E_y-B}$分别是$x$，$y$量化后对应的浮点数，忽略量化误差，有：

$$ 2\log_2(1+\frac{M_y}{L})+2(E_y-B)=-\log_2(1+\frac{M_x}{L})-(E_x-B) $$

由上一节的结论，有：

$$\begin{aligned}
2(\frac{M_y}{L}+\sigma)+2(E_y-B)=-(\frac{M_x}{L}+\sigma)-(E_x-B)\\
(E_yL+M_y)=\frac{3}{2}L(B-\sigma)-\frac{1}{2}(E_xL+M_x)
\end{aligned}$$

这里$(E_yL+M_y)$和$(E_xL+M_x)$正好和整形变量i对应，而$3L(B-\sigma)/2$是个常数，记作$K$.上式能很好地和i = 0x5f3759df - (i \>> 1)对应起来.这就解释了为什么估计迭代初值可以是这种形式.反解0x5f3759df对应的$\sigma$可得，$\sigma=0.0450461875791687011756$.值得注意的是，此时$\sigma$和$\frac{\pi}{6}\zeta$比较接近.

## 魔法数字的确定
### 最小化线性近似函数的误差
用线性函数$y=x+\sigma$来近似对数函数$y=\log2 (1+x)$，需要调整$\sigma$的取值，使得两函数的函数值在(0,1)区间内的误差值最小，也就是求以下这个优化问题：

$$\sigma^\ast=\underset{\sigma}{\arg\min}\,\sum_{x\in S}\bigl|\log_2 (1+x)-(x+\sigma)\bigr|^2$$

其中 $ S = \\{ 0,1/2^l,\cdots,(2^l-1)/2^l \\} $

用积分代替求和：

$$\begin{aligned}
I=&\int_0^1\left(\log_2 (1+x)-x-\sigma\right)^2 \mathrm{d}x\\
=&\int_0^1\left(\log_2 (1+x)-x\right)^2 \mathrm{d}x -2\sigma\int_0^1\left(\log_2 (1+x)-x\right)\mathrm{d}x+\sigma^2
\end{aligned}$$

令梯度为0：

$$
\frac{\partial{I}}{\partial{\sigma}}=
-2\int_0^1\left(\log_2 (1+x)-x\right)\mathrm{d}x+2\sigma = 0
$$

解得$\sigma^\ast$：

$$\begin{aligned}
\sigma^\ast =&\int_0^1\bigl(\log_2 (1+x)-x\bigr)\mathrm{d}x\\
=&\frac{1}{\ln 2}\bigl((1+x)\ln(1+x)-x\bigr) \Big|_0^1 -\frac{1}{2}\\
=&\frac{2\ln{2}-1}{\ln2}-\frac{1}{2}=\frac{3}{2}-\log_2 \mathrm{e}\\
=&0.05730495911103661
\end{aligned}$$

此时魔法数字，即常数K=0x5f34ff59.此方法得到的$\sigma^\ast$是线性近似函数的最优解，可能并不是最终计算结果误差的最优解.


### 最小化估计结果的误差
用i = K - (i \>> 1)估计迭代初值进行了移位运算.当浮点数的指数编码是奇数时，尾数部分会被移入1从而导致尾数增加0.5.因此，尾数一样并不能保证平方根倒数结果的尾数也一样.事实上不能证明，只有尾数一样且指数模2同余的情况下，结果的尾数才有一样.

为了最小化估计的误差，需要计算所有误差之和，由于即使是32位的单精度浮点数，其数目也比较庞大，我们希望只选取一部分浮点数来统计误差.这部分浮点数集为

$$\begin{aligned}
F=&\left\{(1+m)\cdot 2^{e}\Big|m=\frac{k}{2^l}\in[0,1),\,k\in\{0,1,\cdots,2^l-1\},\,e\in\{0,1\}\right\}\\
=&\left\{1+m\big|m\in[0,1)\right\} \cup \left\{2(1+m)\big|m\in[0,1)\right\}\\
\triangleq &F_1 \cup F_2
\end{aligned}$$

由$\sigma\in(0, \zeta)$，K的取值范围为0x5f2f796c~0x5f400000，因此K对应的浮点数的符号位为0.

设$K = \\{0,E_K,M_K\\}$. 令$x=(1+m_x)\cdot 2^{E_x-B}$，
则$x \>> 2$对应的浮点数为

$$\bigl(1+\lfloor m_x/2\rfloor+r\bigr)\cdot 2^{\lfloor E_x/2\rfloor-B},\quad r = 0.5\cdot(E_x\mod 2)$$

计算x的平方根倒数的真实值y，得

$$\begin{aligned}
y=&x^{-1/2}=(1+m_x)^{-1/2}\cdot 2^{-E_x/2+B/2}\\
E_y=&[-E_x/2+B/2+B]\\
1+m_y=&(1+m_x)^{-\frac{1}{2}}
\end{aligned}$$

### 指数部分

$$E_K^\ast=\underset{E_K}{\arg\min}\,
\sum_{x\in F}\Bigl|E_K - B - (\lfloor E_x/2\rfloor-B)-(-E_x/2+B/2+B)\Bigr|^2$$

$$\begin{aligned}
&\Bigl|E_K-\lfloor E_x/2\rfloor-(-E_x/2+B/2+B)\Bigr|\\
=&\Bigl|E_K-\lfloor E_x/2\rfloor+E_x/2-3B/2\Bigr|\\
\end{aligned}$$

因为$E_x/2$的期望为$\lfloor E_x/2\rfloor+1/4$

$$\therefore\quad E_K\ast=\bigl[3B/2-1/4\bigr]=\bigl\lfloor 3B/2\bigr\rfloor=
\bigl\lfloor 3\times 127/2\bigr\rfloor=190=\text{0xbe}$$

0xbe \>> 1 = 0x5f

### 尾数部分
$\lfloor m_x/2\rfloor+r$ 的期望是 $\lfloor m_x/2\rfloor+1/4\approx m_x/2+1/4$

$$\begin{aligned}
m_K^\ast= &\underset{M_K}{\arg\min}\,
\sum_{x\in F}\Bigl|m_K-(\lfloor m_x/2\rfloor+r)-m_y\Bigr|^2\\
=&\underset{M_K}{\arg\min}\,
\sum_{x\in F_1}\Bigl|m_K-(m_x/2+1/4)-m_y\Bigr|^2
\end{aligned}$$

令梯度等于零，得：

$$\begin{aligned}
m_K=&\frac{\sum_{x\in F_1}(m_x/2+1/4+m_y)}{\sum_{x\in F_1} 1}\\
=&\frac{\sum_{m_x\in [0,1)}(m_x/2+1/4+m_y)}{\sum_{m_x\in [0,1)} 1}\\
\approx&\int_0^1 \left(\frac{x}{2}+(1+x)^{-\frac{1}{2}}-1+\frac{1}{4}\right)\mathrm{d}x\\
=&\frac{1}{4}+2(1+x)^{\frac{1}{2}} \big|_0^1 - \frac{3}{4}\\
=&0.3284271247461903
\end{aligned}$$

优化相对误差，利用python的scipy.integrate.quad函数可求得：

$$\begin{aligned}
m_K=&\frac{\displaystyle
\int_0^1 \frac{x/2+(1+x)^{-1/2}-1+1/4}
{(1+x)^{-1/2}-1}\mathrm{d}x}
{\displaystyle\int_0^1 \frac{1}{(1+x)^{-1/2}-1}\mathrm{d}x}\\
=&0.26236034051862456
\end{aligned}$$

以上的模型还是太过粗糙，以致$m_K$和理论上的最佳值相差较大。Chris Lomont在论文 _Fast Inverse Square Root_ 中，给出了更加复杂的误差模型，并通过程序求出了这个值

$$m_K\ast=0.432744889959443195468521587014$$

## 总结
本文主要分析了平方根倒数的速算法的源代码及其中魔法数字0x5f3759df的由来。经过我各种失败的尝试，都无法得到0x5f3759df这个常数。源代码中再得到迭代的初始值后，又进行了一次牛顿迭代提高精度，但本文的优化方法都是对迭代初值的优化，得到的常数K不能保证经过一次牛顿迭代后，总体的误差是最优的，通过暴力遍历找到最优的常数K看起来更加简便可行。

值得注意的是，在Chris Lomont的演算中，理论上最优常数是0x5f37642f，并且在实际测试中，如果只使用一次迭代的话，其效果也是最好的。但奇怪的是，经过两次牛顿迭代后，在该常数下解的精度将降低得非常厉害。经过实际的测试，Chris Lomont认为，最优常数是0x5f375a86。如果换成64位的double版本的话，算法还是一样的，而最优常数则为0x5fe6ec85e7de30da。
