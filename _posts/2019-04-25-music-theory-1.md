---
layout: post
title: 音乐与数学 (1) 纯音，泛音和拍音
date:  2019-04-25
categories: blog
tags: [Music Theory]
description: 
---

## 纯音

从信号分解的角度看，最基本的声波也是正弦波，任何声波可以被分解成频域空间，连续频率的正弦信号的线性组合。正弦波的声音就是[**纯音(Pure tone)**](https://en.wikipedia.org/wiki/Pure_tone)，但是可以被人耳听到的纯音在自然界中几乎不存在，因为简单的机械运动频率太低。我们可以用计算机人工合成一个纯音（实验1.1），它的频率为$\omega=440 \text{Hz}$，持续时间为$1$s，声波的振动运动方程为：

$$
f(t) = Asin(2\pi\omega t + \varphi)
$$

改变参数$\varphi$发现，这段正弦波听起来没有多大的差别。正弦波听起来非常的单调，在任何时刻听起来都一样，如果我们从任意的中间时刻开始播放这段音频，相当于改变了相位，但是显然，正弦波的声音不会有任何改变。这个相位无关的结论对于多个正弦波合成的波形声音也是成立的，读者可以运行实验1.2得到验证。因此为了方便起见，在以后的实验中，我们不再考虑相位参数。

## 泛音

按下钢琴的琴键，可以听到钢琴发出的声音音色比纯音更加饱满、悦耳，这是因为钢琴发出的声音是含有多个不同比例的纯音。实验1.3对钢琴小字一组的A键发出的声音（基波频率为$f_0=440\text{Hz}$）进行了傅里叶变换，并取模长得到的频谱图。这段音频来源于[音色库midi-js-soundfonts](https://github.com/gleitz/midi-js-soundfonts)，时长约2.5s，第一张图是它的波形图，第二张图是其对应的0-8kHz范围内的频谱，第三张图片是0-1.2kHz范围内的频谱。


[![](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/music_theory/piano_a4.png)](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/music_theory/piano_a4.svg)


观察钢琴音色的频谱图可以发现若干个等距的高峰，分别是$440\text{Hz},880\text{Hz},1320\text{Hz},\dots$。其中第一个高峰的能量最强，是琴弦振动的固有频率，这个成分称为基波，基波的频率称为基频。后续出现的高峰的频率是基频的整数倍，称为谐波，音乐人往往将谐波称为[**泛音(Overtone)**](https://en.wikipedia.org/wiki/Overtone)。我们认为音高的频率是基波的频率。

泛音的成因有理论上的解释，是个经典的物理模型，可参考[*有界弦的自由振动问题*](https://en.wikipedia.org/wiki/String_vibration)。在有明显音高的乐器中，弦或空气管之间会有因反射而产生干涉，并形成驻波。关于驻波的通俗解释，可以参考[李永乐老师讲神奇的驻波实验](https://www.bilibili.com/video/av43117270)。

## 拍音

[**拍音**(Beat tone)](https://en.wikipedia.org/wiki/Beat_(acoustics))是另一种复合音，它是由来自同一种乐器或不同乐器的两个单音相互叠加，形成具有规律性强弱变化的振动。与谐波不同的是，拍音一般要求这两个音的振幅相近，但不要求频率为倍数关系。

由于单音本身就由纯音叠加而来，再将单音相互叠加，情况将会变得非常复杂。为方便描述，下文将以纯音的叠加来解释拍音的形成。实验1.4演示了拍音形成的过程。两个单音的频率分别为$f_1=260\text{Hz}$和$f_2=241\text{Hz}$，则周期之比为$T_1/T_2 = 241/260\approx 12/13$。此时不太精确的人耳就会认为最小的公共周期是$T = 13T_1 = 12T_2$，抖动的频率为$f=12f_1=13f_2$

[![](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/music_theory/beat_tone.png)](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/music_theory/beat_tone.svg)

特别地，频率相差一倍的两个单音叠加形成的拍音，其频率等于较低的音的频率，这样的拍音听起来就像一个音。当两个单音频率很接近时，拍音的频率变成了两个单音的平均值，而拍频变得很小，周期很长，可以听到声音的音量逐渐地由强变弱、由弱变强，利用这一特性可以进行调音（调整后，和标准音叉形成的和声拍音，周期接近无穷大）。

频率接近的和声，频率听起来是平均值的证明如下：
先令振幅相等，利用和差化积公式，

$$\begin{aligned}
y = &\frac{1}{2}\left(\sin(\omega_1t)+\sin(\omega_2t)\right)
=\sin\left(\frac{\omega_1+\omega_2}{2}\right)\cos\left(\frac{\omega_1-\omega_2}{2}\right)\\
\approx &\sin\left(\frac{\omega_1+\omega_2}{2}\right)\quad \text{when}\ \omega_1 \approx \omega_2
\end{aligned}$$

再令振幅一般化，取$0< \Delta a < \epsilon$，$n_1=\lfloor A_1/\Delta a\rfloor$，$n_2=\lfloor A_2/\Delta a\rfloor$，$m=\lfloor\log_2(n_1+n_2)\rfloor$，

$$\begin{aligned}
2y = &A_1\sin(\omega_1t)+A_2\sin(\omega_2t)
\approx \Delta a \left(n_1\sin(\omega_1t)+n_2\sin(\omega_2t)\right)\\
=& \Delta a \sum ^{n_1+n_2}_{i=1}\sin(\omega_it)
\approx \Delta a \sum ^{2^m}_{i=1}\sin(\omega_it)\\
\approx &2\bar{A}\sin(\bar\omega t)
\end{aligned}$$
