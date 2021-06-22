---

title: CodeMi 108 猜数字
slug: codemi108
date: 2019-05-02 15:30:00 UTC+08:00
tags: 思维
category: OI
author: yilanxuan
has_math: true

---

经典题。

<!-- TEASER_END:  -->

[传送门](https://code.mi.com/problem/list/view?id=108)

## 题意

相传，十八世纪的数学家喜欢玩一种猜数字的小游戏，规则如下： 首先裁判选定一个正整数数字 N(2≤N≤200)，然后选择两个不同的整数 X,Y(1≤X≤Y≤N) 裁判告诉玩家S这两个数字的和；告诉玩家P这两个数字的乘积 由玩家S开始，双方依次告诉裁判自己是否知道X, Y分别是多少，如果有一方知道，那么游戏就结束了！

例如，裁判先选定 N=10 并将 N 的值告诉玩家，然后从 1～N 中选择X=3,Y=6，并将它们的总和9告诉给玩家S，将它们的乘积18告诉给玩家P：

玩家S：“我不知道这些数字”

玩家P：“我不知道这些数字”

玩家S：“我不知道这些数字”

玩家P：“我不知道这些数字”

玩家S：“哦，我知道这些数字了，他们是3和6”

数学家们都非常的聪明，他们总能用最少的次数推断出这些数字。

现在给定 N 和 M  (0≤M≤100)，M为玩家回答“我不知道这些数字”的次数，请你给出裁判选择的X,Y的组合有多少种？

## 做法

枚举这两个数字是什么，然后模拟过程。

A 有一些可能状态，可以求出这些可能状态需要多少次推出来。

我们这样可以记忆化。

当然也可以逆着来。我们先把 0 次可以求出来的状态全给找出来，然后在其他里面删除这个状态。

然后重复这个过程。

## 代码

```cpp
#define pr pair<int, int>
#define fi first
#define se second
#define mp make_pair
const int maxm = 200*200+10;
const int maxn = 402;
int n, m;
set<pr> a[maxn];
set<pr> b[maxm];
int f[maxn][maxn];
int sza[maxn], szb[maxm];
int main()
{
    n = read(), m = read();
    for (int i = 1; i <= n; i++)
        for (int j = i + 1; j <= n; j++)
        {
            a[i + j].insert(mp(i, j));
            b[i * j].insert(mp(i, j));
            sza[i + j]++;
            szb[i * j]++;
        }
    memset(f, -1, sizeof(f));
    for (int T = 0; T <= m; T++)
    {
        if (T & 1)
        {
            for (int i = 1, x, y; i <= n * n; i++)
                if (szb[i] == 1)
                    for (auto v : b[i])
                    {
                        x = v.fi;
                        y = v.se;
                        if (f[x][y] == -1)
                        {
                            f[x][y] = T;
                            sza[x + y]--;
                            szb[x * y]--;
                            b[i].clear();
                            a[x + y].erase(a[x + y].find(mp(x, y)));
                            break;
                        }
                    }
        }
        else
        {
            for (int i = 1, x, y; i <= n + n; i++)
                if (sza[i] == 1)
                {
                    for (auto v : a[i])
                    {
                        x = v.fi;
                        y = v.se;
                        if (f[x][y] == -1)
                        {
                            f[x][y] = T;
                            sza[x + y]--;
                            szb[x * y]--;
                            a[i].clear();
                            b[x * y].erase(b[x * y].find(mp(x, y)));
                            break;
                        }
                    }
                }
        }
    }
    ll ans = 0;
    for (int i = 1; i <= n; i++)
    {
        for (int j = i + 1; j <= n; j++)
            if (f[i][j] == m)
                ans++;
    }
    write(ans);
    return 0;
}
```

