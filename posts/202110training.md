---

title: 2021 十月乱做
slug: 202110training
date: 2021-10-01 12:00:00 UTC+08:00
tags: 思维, dp, 图论, 最短路, floyd
category:  OI
author: yilanxuan
has_math: true

---

记录十月份写的一些题。

<!-- TEASER_END:  -->

## P1875 佳佳的魔法药水

### 题意

[传送门](https://www.luogu.com.cn/problem/P1875)

### 做法 

考虑一种暴力做法：$f_i$ 表示合成药水 $i$ 需要的最小的花费 。

对于每个合成方法$(x,y)\to z$，进行一次更新为 $f_{z}=\min(f_z,f_x+f_y)$，如果更新了，就把 $f_{z}$ 加入优先队列，对与之相连的点进行更新。

因为涉及统计方案数，可能会重，所以对 $f_z$ 进行更新的条件是 $f_x,f_y$ 已经是最小值了（即在优先队列中出队了）。

时间复杂度： 我不知道，大约是正常复杂度。 

```cpp
#include <bits/stdc++.h>
#define ll long long
using namespace std;
void qmax(int &x, int y)
{
    if (x < y)
        x = y;
}
void qmin(int &x, int y)
{
    if (x > y)
        x = y;
}
inline ll read()
{
    char s;
    ll k = 0, base = 1;
    while ((s = getchar()) != '-' && s != EOF && !(isdigit(s)))
        ;
    if (s == EOF)
        exit(0);
    if (s == '-')
        base = -1, s = getchar();
    while (isdigit(s))
    {
        k = k * 10 + (s ^ '0');
        s = getchar();
    }
    return k * base;
}
inline void write(int x)
{
    static char cnt, num[15];
    cnt = 0;
    if (!x)
    {
        printf("0");
        return;
    }
    for (; x; x /= 10)
        num[++cnt] = x % 10;
    for (; cnt; putchar(num[cnt--] + 48))
        ;
}
int n, m, s;
const int maxn = 1e3 + 100;
const int maxm = 2e5 + 100;
int po[maxn], to[maxm], ne[maxm], w[maxm], id;
int dis[maxn];
ll f[maxn];
void add(int x, int y, int z)
{
    id++;
    to[id] = y, w[id] = z, ne[id] = po[x], po[x] = id;
}
struct node
{
    int x, d;
    bool operator<(const node &A) const
    {
        return A.x < x;
    }
};
priority_queue<node> q;
int vis[maxn];
int main()
{
#ifdef ylx
    freopen("p1875.in", "r", stdin);
    freopen("p1875.out", "w", stdout);
#endif
    n = read(); //, m = read(), s = read();
    n--;
    for (int i = 0; i <= n; i++)
    {
        dis[i] = read();
        f[i] = 1;
        q.push((node){dis[i], i});
    }
    int x, y, z;
    while (scanf("%d%d%d", &x, &y, &z) != EOF)
    {
        add(x, y, z);
        if (x == y)
            continue;
        add(y, x, z);
    }
    int u, v;
    node c;
    while (!q.empty())
    {
        c = q.top();
        q.pop();
        if (c.x != dis[c.d])
            continue;
        u = c.d;
        vis[c.d] = 1;
        for (int i = po[u]; i; i = ne[i])
        {
            v = to[i];
            if (!vis[v])
                continue;
            if (dis[v] + dis[u] < dis[w[i]])
            {
                dis[w[i]] = dis[u] + dis[v];
                f[w[i]] = f[u] * f[v];
                q.push((node){dis[w[i]], w[i]});
            }
            else if (dis[v] + dis[u] == dis[w[i]])
                f[w[i]] += f[u] * f[v];
        }
    }
    for (int i = 0; i <= 0; i++)
        printf("%d %lld\n", dis[i], f[i]);
    return 0;
}
```

## P2047 [NOI2007] 社交网络

### 题意

[传送门](https://www.luogu.com.cn/problem/P2047)

### 做法

$$
I(v)=\sum_{s \ne v,t\ne v} \frac{C_{s,t}(v)}{C_{s,t}}
$$

跑 `floyd` 进行松弛操作的时候随手统计方案数，然后对于每个点，都枚举一下 $s,t$。

时间复杂度 $\Theta(n)$。

## gym 103102

10-4 下午开的两套题，写了两个 dp。

### E

**题意**

给你一个 $n\le5\times 10^5$ 的序列，求有多少个区间 $[l,r]$ 满足 $\sum_{i=l}^r\sum_{j=i+1}^r a_i\times a_j$ 是 3 的倍数。

**做法**

只有 0,1,2 三种数，0 完全不用管。

考虑对每个位置 $i$ 找到有多少个合法左端点。

$$
\sum_{i=2}^n \sum_{j=1}^{i-1} a_{i}\times a_j=\sum_{i=2}^n a_i \sum_{j=1}^{i-1} a_j
$$

可以发现，有效信息只有两个：

1. 目前贡献对 3 取模余数是几。
2. 目前式子的右半部分对 3 取模是几。

$ dp_{i,0/1/2,0/1/2} $表示到第 $i$ 位，最终贡献为 0/1/2，前面一堆前缀和是 0/1/2 的方案数。

然后转移一下就好了。

### F

**题意**

给你一个长度为 $n(n\le 3000)$ 的排列，然后对其进行染色，每次可以将一个区间 $[l,r]$ 染成 $\min\limits_{i=l}^r a_i$，求状态数（包含初始状态）。

**做法**

$dp_{i,j}$ 表示考虑了前 $i$ 个数字，结尾是 $j$ 的方案数。$p_j$ 表示数字 $j$ 出现的位置。

1. $p_j=i$ 上一个结尾可以是任何出现在 $j$ 之前的数和 $j$ 自身。

2. $p_j<i$ 考虑 $j$ 从前面来的，所以上一位一定也是 $j$，或者是比 $j$ 小而且出现在 $j$ 前面的。

3. $p_j > i$ 考虑 $j$ 从后面来的，首先得能过来，如果过得来，上一位数 $x$ 应当满足 $p_x\le  p_j$ （如果 $x$ 在 $j$ 后面则肯定不可以）

**代码**

```cpp
const int maxn = 3e3 + 10;
const ll mod = 1e9 + 7;
int n;
int a[maxn], p[maxn];
ll dp[maxn][maxn], f[maxn][maxn]; //f[i][j] 表示位置在 j 以内的dp值的前缀和
ll g[maxn];
int l[maxn], r[maxn];
int main()
{
#ifdef ylx
    freopen("F.in", "r", stdin);
    freopen("F.out", "w", stdout);
#endif
    n = read();
    for (int i = 1; i <= n; i++)
        a[i] = read(), p[a[i]] = i;
    for (int i = 1; i <= n; i++)
    {
        l[i] = r[i] = p[i];
        while (l[i] > 1 && a[l[i] - 1] > i)
            l[i]--;
        while (r[i] < n && a[r[i] + 1] > i)
            r[i]++;
    }
    for (int i = 1; i <= n; i++)
    {
        if (l[i] == 1)
            dp[1][i] = 1, f[1][p[i]] = 1; // 寻找合法的开头
        g[i] = dp[1][i];
    }
    for (int i = 1; i <= n; i++)
        g[a[i]] += g[a[l[a[i]] - 1]];
    for (int i = 1; i <= n; i++)
        (f[1][i] += f[1][i - 1]) % mod;
    for (int i = 2; i <= n; i++)
    {
        for (int j = 1; j <= n; j++)
        {
            if (p[j] == i)
                dp[i][j] += f[i - 1][p[j]];
            else if (p[j] < i)
            {
                if (r[j] >= i)
                    dp[i][j] += g[j];
            }
            else
            {
                if (l[j] <= i)
                    dp[i][j] += f[i - 1][p[j]];
            }
            (f[i][p[j]] += dp[i][j]) %= mod;
        }
        memset(g, 0, sizeof(g));
        for (int j = 1; j <= n; j++)
        {
            g[j] = dp[i][j];
            f[i][j] += f[i][j - 1];
            f[i][j] %= mod;
        }
        for (int j = 1; j <= n; j++)
        {
            g[a[j]] += g[a[l[a[j]] - 1]];
            g[a[j]] %= mod;
        }
    }
    printf("%lld\n", f[n][n]);
    return 0;
}
```

## World Final 2021 Gene Folding

### 题意

给定一个字符串 $s(|s|\le 4\times 10^6)$，每次可以把一个长度为偶数的回文前缀或后缀折叠，保留靠近主串的那一半，求经过若干次折叠之后，字符串长度最短为多少。

例如：$AABAABA$  第一次折叠后为 $ABAABA$，第二次折叠后为 $ABA$，所以答案为 3。

### 做法

首先，使用 $Manacher$ 算法对于每个位置 $i$ 求出 $f_i$，表示以 $i$ 为中心，可以拓展到的回文串的最大长度的一半（向上取整）。

然后从左到右进行贪心，每次找到最长的一个左端点为当前字符串开头的位置的回文串（长度为偶数）进行折叠，改变字符串开头位置。重复这个过程，直到无法折叠。从右往左进行贪心，折叠。最终可以求得最后保留的字符串区间 $[l,r]$ 。

不存在一种情况：左边折叠一次，右边折叠一次，然后左边产生新的折叠。根据回文串的性质，可以发现这种情况可以直接在左边进行 2 次折叠。

### 代码

```cpp
#include <bits/stdc++.h>
#define ll long long
using namespace std;
int qmax(int &x, int y)
{
    if (x < y)
        x = y;
}
int qmin(int &x, int y)
{
    if (x > y)
        x = y;
}
int read()
{
    char s;
    int k = 0, base = 1;
    while ((s = getchar()) != '-' && s != EOF && !(isdigit(s)))
        ;
    if (s == '-')
        base = -1, s = getchar();
    while (isdigit(s))
    {
        k = k * 10 + (s ^ '0');
        s = getchar();
    }
    return k * base;
}
void write(int x)
{
    if (x < 0)
    {
        putchar('-');
        write(-x);
    }
    else
    {
        if (x / 10)
            write(x / 10);
        putchar(x % 10 + '0');
    }
}
const int maxn = 5000000 * 2 + 100;
const int N = 5000000 + 100;
char ch[N], c[maxn];
int n, maxl, maxr, mid, f[maxn], ans;
int g[maxn]; // g[i] 表示 左端点到 i 的回文串中最右的一个。
int h[maxn]; // h[i] 表示 右端点到 i 的回文串中最左的一个
int main()
{
#ifdef ylx
    freopen("genefolding.in", "r", stdin);
    freopen("genefolding.out", "w", stdout);
#endif
    scanf("%s", ch + 1);
    n = strlen(ch + 1);
    for (int i = 1; i <= n; i++)
        c[i * 2 - 1] = '#', c[i * 2] = ch[i];
    n = n * 2 + 1;
    c[n] = '#', c[0] = '#', c[n + 1] = '0';
    for (int i = 2; i < n; i++)
    {
        if (i < maxr) f[i] = min(f[mid * 2 - i], f[mid] + mid - i);
        else f[i] = 1;
        while (i - f[i] > 0 && c[i - f[i]] == c[i + f[i]]) f[i]++;
        if (i + f[i] > maxr)
        {
            maxr = f[i] + i;
            maxl = i - f[i];
            mid = i;
        }
        ans = max(ans, f[i]);
    }
    int now = n;
    for (int i = n; i >= 1; i--)
    {
        if (c[i] != '#') continue;
        if (i - f[i] + 1 <= now)
        {
            while (now > i) g[now] = 0, --now;
            while (now > 0)
            {
                g[now] = i;
                if (c[now] != '#') g[now] = 0;
                now--;
                if (now < i - f[i] + 1) break;
            }
        }
    }
    int l = 1, r = n;
    while (true)
    {
        if (g[l] == l || g[l] == 0) break;
        int x = g[l]; //合并到l
        l = x;
    }
    now = l;
    for (int i = l; i <= n; i++)
        f[i] = min(f[i], i - l + 1);
    for (int i = l; i <= n; i++)
    {
        if (c[i] != '#')
            continue;
        if (i + f[i] - 1 >= now)
        {
            while (now < i) ++now;
            while (now <= n)
            {
                if (c[now] == '#') h[now] = i;
                now++;
                if (now > i + f[i] - 1) break;
            }
        }
    }
    while (true)
    {
        if (h[r] == r || h[r] == 0) break;
        r = h[r];
    }
    cout << (r - l + 1) / 2;
    return 0;
}
```

## CF1553E

### 题意

一个长度为 $n$ 的初始排列为 $[1,2,3,4,\ldots,n]$ 。
对其进行下列操作：

* 首先，我们将其循环移动 $k$ 位， $k$ 为一个未知数 $(0 \leq k \leq n-1)$ 。

将一个长度为 $n$ 的数组循环移动k位就是将原数组最后 $k$ 位移动到第 $1 \sim k$ 位，并将其余 $n-k$ 位移动到第 $k+1 \sim n$ 位。比如说，我们将 $[1,2,3,4,5,6]$ 循环移动两位，就是 $[5,6,1,2,3,4]$ 。

* 然后，我们将数组中任意两个数交换，最多进行 $m(0 \leq m \leq \dfrac{n}{3})$ 次。

给定 $n,m$ 和最后的结果，你需要找出所有可能的 $k$ 值。

多测，所有测试数据的 $n$ 之和不超过 $3 \times 10^5$。

### 做法

把环拆成链，然后每个数字只有一个位置是不用动的，考虑区间开头往后移动。

最多可以 $2m$ 个数不在位置上。 $m\le \frac{n}{3}$，$2m\le \frac{2n}{3}$，因此至少有 $\frac{n}{3}$ 个数在准确位置上的 $k$ 才可能有效，从而可以知道合法的 $k$ 不超过 3 个。

判断一个 $k$ 是否合法：考虑一个大小为 $x$ 的环，需要 $x-1$ 次操作。

接着直接暴力判断每个 $k$ 是否可行。

### 代码

```cpp
/*
 * Author: ylx
 * Lang: c++
 * Prog: E
 */
#include <bits/stdc++.h>
#define ll long long
using namespace std;
void qmax(int &x, int y)
{
    if (x < y)
        x = y;
}
void qmin(int &x, int y)
{
    if (x > y)
        x = y;
}
inline ll read()
{
    char s;
    ll k = 0, base = 1;
    while ((s = getchar()) != '-' && s != EOF && !(isdigit(s)))
        ;
    if (s == EOF)
        exit(0);
    if (s == '-')
        base = -1, s = getchar();
    while (isdigit(s))
        k = k * 10 + (s ^ '0'), s = getchar();
    return k * base;
}
inline void write(ll x)
{
    static char cnt, num[20];
    cnt = 0;
    if (!x)
    {
        putchar('0');
        return;
    }
    for (; x; x /= 10)
        num[++cnt] = x % 10;
    for (; cnt; putchar(num[cnt--] + 48))
        ;
}
const int maxn = 3e5 + 10;
int T, n, m;
int a[maxn], p[maxn]; // p[i] 表示数字 i 要到达哪个位置。
int b[maxn], cnt[maxn];
int ans[10], iid;
int vis[maxn];
int main()
{
#ifdef ylx
    freopen("E.in", "r", stdin);
    freopen("E.out", "w", stdout);
#endif
    T = read();
    while (T--)
    {
        iid = 0;
        n = read(), m = read();
        for (int i = 0; i <= n; i++)
            cnt[i] = 0;
        for (int i = 1; i <= n; i++)
        {
            a[i] = read();
            b[i] = i - a[i];
            if (b[i] < 0)
                b[i] += n;
            cnt[b[i]]++;
        }
        for (int k = 0; k < n; k++) //a[i]=b[i+k]
            if ((n - cnt[k]) <= 2 * m)
            {
                int ip = 0;
                for (int i = k + 1; i <= n; i++)
                    p[++ip] = i;
                for (int i = 1; i <= k; i++)
                    p[++ip] = i;
                for (int i = 1; i <= n; i++)
                    vis[i] = 0;
                int sum = 0; //sum:环数
                for (int i = 1; i <= n; i++)
                    if (!vis[i])
                    {
                        int now = i;
                        while (!vis[now])
                        {
                            vis[now] = 1;
                            now = p[a[now]];
                        }
                        sum++;
                    }
                if (n - sum <= m)
                    iid++, ans[iid] = k;
            }
        printf("%d ", iid);
        for (int i = 1; i <= iid; i++)
            printf("%d ", ans[i]);
        putchar('\n');
    }
    return 0;
}
```



## 训练方向

得训练一些没有太多人可以通过的图论，数据结构题了。

好像计算几何也是我的。

