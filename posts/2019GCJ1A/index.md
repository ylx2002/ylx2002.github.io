---

title: Google Code Jam Round 1A 乱做
slug: 2019GCJ1A
date: 2019-04-13 11:30:00 UTC+08:00
tags: 构造
category: OI
author: yilanxuan
has_math: true

---

装作自己打了。

<!-- TEASER_END:  -->

[传送门](https://codingcompetitions.withgoogle.com/codejam/round/0000000000051635)


## A

### 题意

给你一个 $n\times m$ 的长方形棋盘，你需要在棋盘中构造出这么一条哈密尔顿路径（注意：不是回路），使得路径上任何相邻两个节点不在同一行、不在同一列，且不在同一对角线上。

Limits

Time limit: 20 seconds per test set.

Memory limit: 1GB.

Test set 1 (Visible)

T = 16.

2 ≤ R ≤ 5.

2 ≤ C ≤ 5.

Test set 2 (Hidden)

1 ≤ T ≤ 100.

2 ≤ R ≤ 20.

2 ≤ C ≤ 20.

Sample

Input 
 	 
```
2
2 2
2 5
```

Output 
 
```
Case #1: IMPOSSIBLE
Case #2: POSSIBLE
2 3
1 1
2 4
1 2
2 5
1 3
2 1
1 5
2 2
1 4
```
  

### 做法

~~不考虑随机搜索。~~


根据 Dirac 定理可以知道， $nm-2n-2m> \frac{n}{2}$ 的时候一定有解。

n<=5,m<=5 的时候跑暴力。

若有一边为 2 的倍数，且另一边长度 >=5 ，则可以如下图一样构造。

![](/images/1.jpg)

设 n<=m，还有n为奇数的情况，可知 m>=5。

对n分类讨论，

n=1 肯定无解，n>=5 可以拆成 3+2x (x为任意正整数），先放偶数的，奇数的结束位置太迷了。

n=3 的情况如图。

![](/images/2.jpg)

只有搜索的情况才会出无解。

### 代码

## B

### 题意

### 做法

### 代码

## C

### 题意

### 做法

### 代码



