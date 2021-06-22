---

title: GXOIGZOI2019 围观
slug: gxgzoi2019
date: 2019-04-29 13:00:00 UTC+08:00
tags: 模拟, 构造, 线段树
category: OI
author: yilanxuan
has_math: true

---

GX今年竟然不是普及组题当省选题了。

~~相当于新疆宁夏那样没有省选。~~

<!-- TEASER_END:  -->

[传送门](https://www.luogu.org/fe/problem/list?tag=36&keyword=GXOI&orderBy=pid&order=asc&page=1)

## 与或和

### 题意

[不需要大意的中文题](https://www.luogu.org/fe/problem/P5300)

### 做法

考虑只有0和1的情况（看能不能按位）

1 1 1 1 1 0
1 1 0 0 0 0
1 1 0 0 0 0 
1 1 1 0 1 0
0 0 0 0 1 0

相当于求有多少个全 0 的矩阵（求or），多少个全 1 的矩阵（求and）

对于某个点 x，轮廓肯定长这样：

```
         ——
        |
      ——
     |
     |
 ————
|         x
```

是单调的。

考虑新加入一个点，肯定覆盖的是某一段，单调栈维护就好。

~~出两问有什么用。~~

### 代码

暂时咕咕咕了。

## 宝牌一大堆

### 题意

[不需要大意的中文题](https://www.luogu.org/fe/problem/P5301)

### 做法

暂时不想看麻将题。

### 代码

咕咕咕了。
