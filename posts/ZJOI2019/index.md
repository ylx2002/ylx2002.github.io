---

title: 余姚中学春游记
slug: ZJOI2019
date: 2019-04-26 13:00:00 UTC+08:00
tags: 
category: 碎碎念
author: yilanxuan
has_math: true

---

希望那些美好不要转瞬即逝，在我的手中悄无声息地溜走。

希望微笑着迎接浪潮，回眸时，不负过去。

虽然注定失去未来，但总希望能留住现在。

<!-- TEASER_END:  -->

---
## Day $-\infty$

被奇怪的事情缠绕，大概在教室里面会舒服很多。还是想去zjoi吧，见可能再也见不到的人。

## Day 0

报道日没有去报道。

早上坐了将近六个小时的高铁才到余(y)姚(y)北(b)。

直接去的宾馆，房间挺大的。（虽然更喜欢衢州那次有阳台，而且和别人房间连起来的。）

下午和doe，lk一起，去万达面基了sooke，memset0，还有两个不认识的。

吃了 我也不知道是什么而且无法描述 的一堆东西，感觉很奇怪，还有真·混合果汁（一堆东西一个榨汁机还行）。

然后到处逛逛，回去真·浪到一点才睡。

## Day 1

上午听课游戏体验还行，坐在zzq旁边了，然后旁边的dt之类的还在谈论zzq（~~他们不知道旁边是zzq可还行~~）。

余姚中学食堂体验不行，不过没去酒店陪廖哥喝酒还是比较舒服的。中午和dt，小司机玩了一个中午。

下午听课游戏体验还行。~~前面 FJWC 题目选讲还行。~~

没有试机还行。

## Day 2

上午听课体验还行，构造题好玩。

余姚中学食堂体验不行。本来打算和zyy他们去他们宾馆耍的，想想他们明天就 day2 还是算了。

下午第一节课听课体验还行，下午第二节课冬眠体验还行。

晚餐`沙县小吃`体验不行。

## Day 3

考试日，六点半就起来了。大概跟着zy从一个迷之地方绕找到了考场。

#### T1

$n<=8$，高斯消元。	

#### T2

$n\le 300, m\le 300$, $n^2$ 枚举城市对，O(m) 枚举语言。直接暴力覆盖一条路径即可。

$n\le 5000, m\le 5000$，可以发现，可以用语言 $i$ 连接的城市为 $x_i,y_i$路径上的所有城市（相互）

$y_i=x_i+1$ 每个城市可以到达的点为该城市可以到达的最远端点（所有方向）
这个在链上还是很好维护的。

~~感觉就是个数据结构大题啊还是先把其他的暴力写完吧。~~

#### T3

一堆直线，然后求一个人下面最多有多少个人。

对于每个点，最多有 $n$ 个交点，对于每个交点需要 $O(n)$ 来check。

对于一个点，最多有 $n$ 个交点，代表某个点从其下方-〉上方/上方-〉下方

## Day $+\infty$

对于很多人来说，就到此为止了。

其实都是挺强的oier。~~可惜是zj的。~~

或许很多人再也见不到了吧，但是留下的回忆不会消失吧。

就像小司机在说说里写的吧。

>   我希望，那些美好不要转瞬即逝，在我的手中悄无声息地溜走。
>
>   我希望微笑着迎接浪潮，回眸时，不负过去。
>
>   虽然注定失去未来，但总希望能留住现在。

---

一些碎碎念

这就是竞赛吧。

赌不起就不要玩。

>   你要静候，再静候，就算失收始终要守

---

orz zyy队长！

cwl小姐姐终于女队了呢。

zzy国家队的话sxd就省队了吧。

很多人的OI生涯到此为止了吧，希望有机会再次与一些人相遇吧。
