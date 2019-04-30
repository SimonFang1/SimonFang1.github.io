---
layout: post
title: 音乐背后的数学原理 (2) 基本音级
date:  2019-04-27
categories: blog
tags: [Music Theory]
description: 
---

## 乐音的音色

我们听到的乐音都是有一个能量最高的基频和一系列泛音组成的，这在前文已经解释过了。人耳对4000Hz左右的声音最敏感，绝大多数乐音的泛音列正处在这个频段，因此，泛音的成分决定了音色的不同。

同一音高的乐音，在不同乐器上的音色不同，这是因为发声构造的差异导致的泛音比例差异。同一乐器的音色特征明显，可认为泛音比例是固定的，但不同音高彼此之间的和谐程度是不同的，其中要以基频相差2倍的音组成的[**和声(Harmony)**](https://en.wikipedia.org/wiki/Harmony)最为和谐。

## 和声的和谐程度

本节尝试借助KL散度的定义，量化两个乐音之间的距离，两个乐音之间的距离越小，相似程度越高，则听起来越和谐。

实验2.1用以下模型模拟乐音， $\{a_n\}_{n=1}^{\infty}$ 是一个趋于零的有界正项数列， 则频率为$f$的乐音的频谱 $F(\omega;f)$。 $\delta$可视为一个冲激函数， 用方差很小的高斯函数代替。

频谱$F(\omega;f)$ 经过归一化， 满足 $\int_{-\infty}^{\infty}\|F(\omega;f)\|\mathrm{d}\omega=1$


$$
|F(\omega;f_0)| = \sum^{\infty}_{k=1}a_k\delta(\omega - kf_0)
$$

令$f_1<f_2$，则频率分别为$f_1,f_2$的两个乐音$p_1,p_2$之间的距离为

$$
D_{\text{KL}}(p_2 \| p_1) = \int_{\infty}^{\infty}|F(\omega;f_1)|\ln\left(\left|\frac{F(\omega;f_1)}{F(\omega;f_2)}\right|\right)\mathrm{d}\omega
$$

实验2.2固定了$f_1$作为标准音的频率，选择不同的$f_2$作为基频合成了测试音，验证了如果 $f_2 - f_1 > \epsilon >0$ ，那么当$f_2=2f_1$时， $D_{\text{KL}}(p_2 \\| p_1)$ 最小。

[![](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/music_theory/distance_200-1600.png)](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/music_theory/distance_200-1600.svg)

实验2.3在图表上直观地呈现了在一倍频率内$[f_0,2f_0]$，合成乐音到最低音$f_0$之间的距离。也运用同样的方法，展示了钢琴小字一组c1到小字二组c2到c1的距离。

[![](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/music_theory/distance_octave_composed.png)](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/music_theory/distance_octave_composed.svg)

[![](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/music_theory/distance_octave_piano.png)](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/music_theory/distance_octave_piano.svg)

## 音律
**音律(Musical temperament)** 是制定音高的标准，只有音高的标准确定了，才能准确地记录旋律。为了方便理解，我们先从 **十二平均律(Twelve-tone equal temperament,12 equal temperament, 12-TET or 12-ET)** 讲起。

### 十二平均律
我们知道频率相差一倍的音听起来最相似，可以把它们当做一种音。十二平均律把2倍平均分成12个均等的部分—— **半音** ，确定了12种不同的音高，以此来组成旋律。显然，平均分成12份是等比分割的，否则再经过12个半音，频率不能翻倍。相邻两个半音的频率之比为$2^{1/12}$， **全音** 间的音高距离等于两个半音。当一个音高的频率确定后，其他所有的音的频率也能随之确定。

C,D,E,F,G,A,B作为七个基本**音级(step)**，在乐音体系中循环使用。除了B、C，E、F之间是半音关系外，其他连续基本音之间都相差了一个全音。由于其余5个半音没有独立的名称，需要借助 **变音记号** 来表示，**升记号 ♯(Sharp)** 把基本音升高一个半音， **降记号♭(Flat)** 把基本音降低一个半音， **重升记号 &#119082; (Double sharp)** 把基本音升高一个全音， **重降记号𝄫 (Double flat)** 把基本音降低一个全音，**还原记号 ♮ (Natural)** 表示将已经升高或降低的音还原。在钢琴中，这七个基本音级在白键，其余五个半音在黑键。音级之间的距离成为 **度(degree)**，下图C-B为一个七度。

<div align="center"><img src="https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/music_theory/piano_keyboard_one_octave.png"></div>


国际标准规定A4 = 440 Hz，下表列出了乐音体系中，部分音名跟频率的关系。

|音名|频率(Hz)|音名|频率(Hz)|音名|频率(Hz)|
|---:|--:|---:|--:|---:|--:|
|$\text{C}3$|130.81|$\text{C}4$|261.63|$\text{C}5$|523.25|
|$\text{C}^{♯}3,{\text{D}}^{♭}3$ |138.59|$\text{C}^{♯}4,\text{D}^{♭}4$ |277.18|$\text{C}^{♯}5,\text{D}^{♭}5$ |554.37|
|$\text{D}3$|146.83|$\text{D}4$|293.66|$\text{D}5$|587.33|
|$\text{D}^{♯}3,\text{E}^{♭}3$ |155.56|${\text{D}}^{♯}4,\text{E}^{♭}4$ |311.13|$\text{D}^{♯}5,\text{E}^{♭}5$ |622.25|
|$\text{E}3$|164.81|$\text{E}4$|329.63|$\text{E}5$|659.26|
|$\text{F}3$|174.61|$\text{F}4$|349.23|$\text{F}5$|698.46|
|$\text{F}^{♯}3,\text{G}^{♯}3$ |185.00|$\text{F}^{♯}4,\text{G}^{♭}4$ |369.99|$\text{F}^{♯}5,\text{G}^{♭}5$ |739.99|
|$\text{G}3$|196.00|$\text{G}4$|392.00|$\text{G}5$|783.99|
|$\text{G}^{♯}3,\text{A}^{♭}3$ |207.65|$\text{G}^{♯}4,\text{A}^{♭}4$ |415.30|$\text{G}^{♯}5,\text{A}^{♭}5$ |830.61|
|$\text{A}3$|220.00|**A4**|**440.00**|$\text{A}5$|880.00|
|$\text{A}^{♯}3,\text{B}^{♭}3$ |233.08|$\text{A}^{♯}4,\text{B}^{♭}4$ |466.16|$\text{A}^{♯}5,\text{B}^{♭}5$ |932.33|
|$\text{B}3$|246.94|$\text{B}4$|493.88|$\text{B}5$|987.77|


下图是标准钢琴的键盘，有88个音高不同的键，是音域最广的乐器之一，覆盖了A0-C8，青色标注的键是中央C，黄色标注的键是标准音A(440Hz)。除此之外的音，因为太低或太高，人耳无法很好地分辨音高，在音乐中几乎是不用的。

[![](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/music_theory/piano_frequencies.png)](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/music_theory/piano_frequencies.svg)


### 三分损益律（五度相生律）

三分损益律是比十二平均律历史更悠久的音律，影响了十二平均律和七个基本音级的成型。

我们知道乐音的是由基音和泛音组成的。基音和第一泛音之间形成 **八度(octave)** 关系，是除基音本身之外和基音最和谐的一个泛音，它控制了基本音级之间的循环。为了确定出八度之间的其他音高，可以用第二泛音生律。

考虑一段均匀的空气管（琴弦受张力影响，因此不用琴弦），它发声的频率为$f$，把这个音作为起始音$P_0$。 第二泛音$3f$是三次谐波，把三次谐波降低一个八度，就得到了一个八度以内对应的音$P_1$，它的频率是$1.5f$，对应空气管的长度变成原来的$2/3$，减少$1/3$。重复这个操作可以下一个音$P_2$，注意到$1.5\times 1.5>2$，因此$P_2$的频率是$P_1$的$3/4$，对应空气管的长度变成上一步的$4/3$，增加$1/3$。这种生律方法称为三分损益法，三分损益律因此得名。由于它生成音级的顺序是五度关系，因此又叫 **五度相生律**。

下面演示如何从起始音得到一个八度内的十二个半音，这里令 1 = C = 宫。

|音高排序|简谱符号|西方音名|五声音名|相对频率|平均律相对频率|
|-------|-------|-------|-------|-------|------------|
| 0 | 1 | C | 宫 | $2^0 3^0=1.000000$   | $2^{0/12}=1.000000$|
| 7 | 5 | G | 徵 | $2^{-1}3^1=1.500000$ | $2^{7/12}=1.498307$|
| 2 | 2 | D | 商 | $2^{-3}3^2=1.125000$ | $2^{2/12}=1.122462$|
| 9 | 6 | A | 羽 | $2^{-4}3^3=1.687500$ | $2^{9/12}=1.681793$|
| 4 | 3 | E | 角 | $2^{-6}3^4=1.265625$ | $2^{4/12}=1.259921$|
|11 | 7 | B | 变宫 | $2^{-7}3^5=1.898438$ | $2^{11/12}=1.887749$|
|6 | ♯4 | G♭ | 变徵 | $2^{-9}3^6=1.423828$ | $2^{6/12}=1.414214$|
|1 | ♯1 | D♭ | 变商 | $2^{-11}3^7=1.067871$ | $2^{1/12}=1.059463$|
|8 | ♯5 | A♭ | 变羽 | $2^{-12}3^8=1.601807$ | $2^{8/12}=1.587401$|
|3 | ♯2 | E♭ | 变角 | $2^{-14}3^9=1.201355$ | $2^{3/12}=1.189207$|
|10 | ♯6 | B♭ | 闰宫（清羽） | $2^{-15}3^{10}=1.802032$ | $2^{10/12}=1.781797$|
|5 | 4 | F | 清角 | $2^{-17}3^{11}=1.351524$ | $2^{5/12}=1.334840$|
|0|1 | C | 宫 | $2^{-19}3^{12}=1.013643$| $2^{0/12}=1.000000$|

注意到$\log_2 3\approx19/12$，经过12次生律之后，得到了和初始频率很接近的音，可认为没有产生新的音级，回到了初始音级。下面分析生成十二个音级在音高上的均匀性。

假设运用k次三分损益法，生成了包括 $k+1$ 个不同的音级 $\\{P_0,P_1,\dots,P_k\\}$ ，把他们的相对频率 $\\{1,f_1,f_2,\dots,f_k,2\\}$ 升序排列，得到数列 $$\{a_n\}_{n=0}^{k+1}$$ 。 取对数并差分 $b_i = \log a_{i+1}- \log a_i$，得到$$\{b_n\}_{n=0}^k$$ 。

下图展示了 $$\{\log a_n\}_{n=0}^{12}$$ 的分布情况，可认为 $$\{a_n\}_{n=0}^{12}$$ 近似是一个等比数列。

<div align="center"><a href="https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/music_theory/log_an.svg"><img src="https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/music_theory/log_an.png"></a></div>

取不同的 $k$， 计算 $$\{ b_n\}$$ 的标准差和中位数的比值，得到的结果如下。可以看到当 $k=4,6,11$时，有三个极小值点，此时分音是相对均匀的，这也是五声音阶，七声音阶，半音阶的由来。关于音阶，会在后面进行详细介绍。

<div align="center"><a href="https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/music_theory/std_by_median.svg"><img src="https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/music_theory/std_by_median.png"></a></div>


### 纯律

观察C4的泛音列

|基音(f)|第一泛音(2f)|第二泛音(3f)|第三泛音(4f)|第四泛音（5f）|
|-|-|-|-|-|
|C4|C5|G5|C6|E6

五度相生律用第二泛音作为生律要素，用到了质数3；而纯律用第四泛音作为生律要素，用到了质数5。
纯律首先从利用三次五度相生律，得到五度排列的四个音，F——C——G——D，再依次寻找F,C,G的第四泛音A,E,B，构成七个基本音级，组成旋律。这样，F-A-C，C-E-G, G-B-D，分别构成一个三和弦。

|音名|相对频率|平均律相对频率|
|-:|-:|-:|
|C|1.000000|1.000000|
|D|9/8=1.125000|1.122462|
|E|5/4=1.250000|1.259921|
|F|4/3=1.333333|1.334840|
|G|3/2=1.500000|1.498307|
|A|5/3=1.666667|1.681793|
|B|15/8=1.875000|1.887749|
