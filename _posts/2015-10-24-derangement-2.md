---
layout: post
title: 错排数（二）
date: 2015-10-24
categories: blog
tags: [组合数学]
description: 编码实现
---

上回介绍了错排数的简记式：

$$!n = \left[\frac{n!}{\mathrm{e}}\right]$$

根据上式，求得$$!n$$的时间复杂度是O(n)，但是涉及到少量浮点运算。
由于浮点数运算本质上可以归结为整数的运算，
因此一定存在一个运行在整数域，时间复杂度不超过O(n)的算法。

## 递推式
记 $$!n$$ 作 $$D_n$$，则有：

$$\begin{split}
&D_n=nD_{n-1}+(-1)^n\\
&D_n=(n-1)\left(D_{n-2}+D_{n-1}\right)\\
\end{split}$$

## 递推式的推导
$$p_n$$为至少有一封信放进正确信封的概率。之前已经得到：

$$p_n=1-\frac{1}{2!}+\frac{1}{3!}-\frac{1}{4!}+\cdots+\left(-1\right)^{n+1}\frac{1}{n!}$$

$$D_n=n!\left(1-p_n\right)$$

对$$p_n$$进行差分，有：

$$\begin{split}
\left(-1\right)^{n+1}\frac{1}{n!}=&p_n-p_{n-1}=
\left(1-p_{n-1}\right)-\left(1-p_n\right)\\
=&\frac{D_{n-1}}{(n-1)!}-\frac{D_n}{n!}\\
\end{split}$$

$$\begin{split}
&\therefore D_n&=nD_{n-1}+(-1)^n\\
&\therefore D_{n-1}&=(n-1)D_{n-2}-(-1)^n\\
&\therefore D_n&=(n-1)\left(D_{n-2}+D_{n-1}\right)\quad n \ge 4\\
\end{split}$$

## c/cpp实现
```c++
typedef unsigned long long INT;

// O(n) time, O(n) space
void derangementNum_all(INT *d, unsigned n) {
	d[0] = d[1] = 0;
	d[2] = 1;
	for (unsigned i = 2; i < n; i++) {
		d[i+1] = i * (d[i-1] + d[i]);
	}
}

// O(n) time, O(1) space
INT derangementNum(unsigned n) {
	INT dn_2 = 0, dn_1 = 1, d_n;
	if (n <= 1) return 0;
	if (n == 2) return 1;
	for (unsigned i = 2; i < n; i++) {
		d_n = i * ( dn_2 + dn_1 );
		dn_2 = dn_1;
		dn_1 = d_n;
	}
	return d_n;
}
```
