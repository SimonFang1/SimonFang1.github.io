---
layout: post
title: 在线性时间内求解错排数
date: 2015-10-22
categories: blog
tags: [组合数学]
description: 全错位排列指即被欧拉称为组合数论的一个妙题的“装错信封问题”。
---

错排 (derangement) 是组合数学中的问题之一。
考虑一个有n个元素的排列，若一个排列中所有的元素都不在自己原来的位置上，
那么这样的排列就称为原排列的一个错排。n个元素的错排数记为$$D_n$$ 或$$!n$$。 

## 定义

错排是指没有元素出现在自己原本位置的排列。
即存在没有不动点的双射 $$\varphi $$:$$S\rightarrow S$$, $$\varphi(i) \ne i$$, $$\forall i \in S$$.

![Derangement4.png](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/blog/Derangement4.png)
Fig.1 The 9 derangements (from 24 permutations) are highlighted.

## “装错信封”问题
这个问题有许多具体的版本，最早研究错排问题的是尼古拉·伯努利和欧拉，因此历史上也称为伯努利-欧拉的装错信封的问题：在写信时将n封信装到n个不同的信封里，有多少种全部装错信封的情况？


## 概率模型

令$$A_i$$对应第$$i$$封信放进正确信封的事件($$i=1,\cdots,n$$)，
$$p_n=\Pr(\cup_{i=1}^n A_i)$$为至少有一封信放进正确信封的概率.

随机地放置信件，则有$$\Pr(A_i)=1/n$$

$$\sum_{i=1}^n\Pr(A_i)=n\cdot\frac{1}{n}=1$$

类似地，有

$$\sum_{i<j}\Pr(A_i\cap A_j)=\binom{n}{2}\frac{1}{n(n-1)}=\frac{1}{2!}$$

$$\sum_{i<j<k}\Pr(A_i\cap A_j\cap A_k)=\binom{n}{3}\frac{1}{n(n-1)(n-2)}=\frac{1}{3!}$$

由容斥原理：

$$
\begin{split}
\Pr(\cup_{i=1}^n A_i)=&\sum_{i=1}^n\Pr(A_i)\\
-&\sum_{i<j}\Pr(A_i\cap A_j)\\
+&\sum_{i<j<k}\Pr(A_i\cap A_j\cap A_k)\\
-&\sum_{i<j<k<l}\Pr(A_i\cap A_j\cap A_k\cap A_l)+\cdots\\
+&\left(-1\right)^{n+1}\Pr(A_1\cap A_2\cap\cdots\cap A_n) 
\end{split}
$$

因此

<!-- $$
\begin{split}
p_n=&1-\frac{1}{2!}+\frac{1}{3!}-\frac{1}{4!}+\cdots+\left(-1\right)^{n+1}\frac{1}{n!}\\
&\sum_{i=1}^{n}\frac{\left(-1\right)^{i+1}}{i!}
\end{split}
$$ -->

$$
\begin{split}
&p_n&=1-\frac{1}{2!}+\frac{1}{3!}-\frac{1}{4!}+\cdots+\left(-1\right)^{n+1}\frac{1}{n!}\\
&!n&=n!\left(1-p_n\right)=n!\sum_{i=2}^n\frac{(-1)^i}{i!}\\
&&=n!\sum_{i=0}^n\frac{(-1)^i}{i!}\\
\end{split}
$$

## 极限
由函数$$\mathrm{e}^x$$在0处的泰勒展式得:

$$
\lim_{n\to\infty}p_n=1-\frac{1}{2!}-\frac{1}{3!}+\frac{1}{4!}+\cdots
=1-\mathrm{e}^{-1}
$$


$$
\lim_{n\to\infty}\frac{!n}{n!}=\frac{1}{\mathrm{e}}\approx 0.3679\cdots\cdots
$$

## 通项

$$
\begin{split}
!n=&n!\sum_{i=0}^n\frac{(-1)^i}{i!}\\
!n=&n!+\sum_{i=1}^{n}(-1)^{i}\binom{n}{i}(n-i)!\\
\end{split}$$

## 递推式

$$!n=n\cdot!(n-1)+(-1)^n$$

## 简记式

$$
!n=\left[\frac{n!}{\mathrm{e}}\right]
=\left\lfloor\frac{n!}{\mathrm{e}}+0.5\right\rfloor
$$

其中$$\left[x\right]$$表示四舍五入函数，$$\left\lfloor x\right\rfloor$$表示向下取整.

这个简化公式可以由之前的通项公式推导出来。事实上，考虑指数函数$$\mathrm{e}^x$$在0处的泰勒展开：

$$
\begin{split}
\mathrm{e}^{-1}=&1+\frac{-1}{1!}+
\frac{(-1)^2}{2!}+\cdots+
\frac{(-1)^n}{n!}+R_n\\
=&\frac{!n}{n!}+R_n\\
R_n=&\frac{\mathrm{e}^{\xi}}{(n+1)!}(-1)^{n+1}
\end{split}
$$

其中$$R_n$$为拉格朗日余项，$$-1<\xi<0$$.

$$
\begin{split}
&\left|R_n\right|
<\frac{\mathrm{e}^0}{(n+1)!}
=\frac{1}{(n+1)!}\\
&\left|\frac{n!}{\mathrm{e}}-!n\right|
=\left|n!R_n\right|
<\frac{1}{n+1}
\end{split}
$$

当$$n\ge2$$时，$$1/(n+1) < 0.5$$，因此

$$
!n=\left[\frac{n!}{\mathrm{e}}\right]
$$

如果n!的求解问题是线性复杂的，并且在高精度运算中，除法和取整运算能在常数时间内完成，
那么上式的计算复杂度也是线性的，时间复杂度为O(n).

