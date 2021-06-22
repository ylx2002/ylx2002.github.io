---

title: 哈密尔顿问题
slug: Hamiltonian
date: 2019-05-06 21:30:00 UTC+08:00
tags: 图论
category: OI
author: yilanxuan
has_math: true

---

刚好写到一个和有关的构造题

这个问题可以归约到哈密顿回路这一个著名的  NP-complete 问题。

<!-- TEASER_END:  -->

[传送门](https://en.wikipedia.org/wiki/Hamiltonian_path)

## Bondy–Chvátal 定理

Bondy–Chvátal theorem operates on the closure cl(G) of a graph G with n vertices, obtained by repeatedly adding a new edge uv connecting a nonadjacent pair of vertices u and v with degree(v) + degree(u) ≥ n until no more pairs with this property can be found.

每次选择两个点满足 degree(v) + degree(u) ≥ N 连边，直到不存在。

## Dirac 定理

A simple graph with n vertices (n ≥ 3) is Hamiltonian if every vertex has degree n / 2 or greater.

设一个无向图中有 N(N>=3) 个节点，若所有节点的度数都大于等于 N/2 ，则哈密尔顿回路一定存在。

## Ore 定理

Let $G$ be a (finite and simple) graph with $n \ge 3$ vertices. We denote by deg $v$ the degree of a vertex $v$ in $G$, i.e. the number of incident edges in $G$ to v. Then, Ore's theorem states that if


$deg_v + deg_w \ge n$ for every pair of distinct non-adjacent vertices $v$ and $w$ of $G$

then $G$ is Hamiltonian.

如果每对不相邻的点的度数和>=N，那么这个图存在密尔顿回路。

## Landau 定理

竞赛图必有哈密尔顿路径

竞赛图有哈密尔顿回路 ⇔ 竞赛图强联通

证明：考虑反证法，假如不存在哈密尔顿路径，那么我们一定能够找到这个图的最长路径，起点为 s,终点为 t。

假设存在一个点 p 不在这条最长路径中，那么一定存在边 (s,p) (p,t)

最长路上一定存在相邻的边(x,y)满足存在边(x,p)(p,y)(包括点s和点t)，所以最长路必须包括点 p 。

## 题目

Please contact lydsy2012@163.com!

BZOJ 4727: [POI2017]Turysta

~~说不定拿天就出一道出来玩玩。~~

## 代码

留坑。

## 参考

https://www.xuebuyuan.com/3203762.html

https://blog.csdn.net/a_crazy_czy/article/details/73611366

https://en.wikipedia.org/wiki/Hamiltonian_path
