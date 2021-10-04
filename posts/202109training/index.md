---

title: 2021 九月乱做
slug: 202109training
date: 2021-09-01 12:00:00 UTC+08:00
tags: 字符串, 莫队, 组合, 暴力, 线段树, 树剖, 倍增, 01trie, 思维, dp
category:  OI
author: yilanxuan
has_math: true

---

记录九月份写的一些题。

<!-- TEASER_END:  -->

## P1381 单词背诵

### 题意

从学员分享里面看到的一个串题。

要背 $n$ 个单词，有一篇 $m$ 长的文章。灵梦想在文章中找出连续的一段，其中包含最多的她想要背的单词（重复的只算一个）。并且在背诵的单词量尽量多的情况下，还要使选出的文章段落尽量短。$n\le 1000,m\le10^5$。

[传送门](https://www.luogu.com.cn/problem/P1381)

### 做法

我直接用的 map，多一个 log 的时间复杂度。从左往右枚举 $r$，找到最大的 $l$，使区间 $[l,r]$ 满足题目条件。

然后 $r,l$ 都是从左往右扫，时间复杂度 $O(m\log m)$。

```cpp
/*
 * Author: ylx
 * Lang: c++
 * Prog: 1
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
const int maxn = 1010;
const int maxm = 1e5 + 10;
int n, m;
string a[maxn], b[maxm];
map<string, int> Map, Mp; // Mp 存这个单词在当前区间出现了多少次
int main()
{
#ifdef ylx
    freopen("p1381.in", "r", stdin);
    freopen("p1381.out", "w", stdout);
#endif
    n = read();
    for (int i = 1; i <= n; i++)
        cin >> a[i], Map[a[i]] = 1;
    m = read();
    int ss = 0;
    for (int i = 1; i <= m; i++)
    {
        cin >> b[i];
        if (Map[b[i]] && !Mp[b[i]])
        {
            ss++;
            Mp[b[i]] = 1;
        }
    }
    Mp.clear();
    if (ss == 0)
    {
        printf("0\n0");
        return 0;
    }
    int cnt = 0, l = 1, ans = m;
    for (int i = 1; i <= m; i++)
    {
        if (Map[b[i]])
        {
            if (!Mp[b[i]])
                cnt++;
            Mp[b[i]]++;
        }
        while (l <= i && (Mp[b[l]] > 1 || (!Map[b[l]])))
        {
            Mp[b[l]]--;
            l++;
        }
        if (cnt == ss)
            qmin(ans, i - l + 1);
    }
    printf("%d\n%d\n", ss, ans);
    return 0;
}
```

## HDU 6333 Problem B. Harvest of Apples

周末开了一场训练，结果 HDUOJ 为了压力测试把题目的提交关了。

[Problem - 6333 (hdu.edu.cn)](https://acm.hdu.edu.cn/showproblem.php?pid=6333)

### 题意

$n$ 个苹果，编号为 $1,2,3,\dots,n$，统计有多少种方法取**至多** $m$ 个苹果。

### 做法

显然，这题是求 $\sum_{i=0}^m \binom{n}{i} $ ，设 $f_{n,m}$ 表示着玩意。

两个转移：$f_{n,m+1}=f_{n,m}+\binom{n}{m+1}$

$ f_{n+1,m}=2\times f_{n,m}-\binom{n}{m} $

然后就有一种分块的套路，预处理出所有的 $f_{x,y}$ 满足 $x=k\times\lfloor n\rfloor\ (n=10^5,k\in \N)$，$y=k\times\lfloor n\rfloor\ (n=10^5,k\in \N)$。

然后分块多么的不好写。就直接莫队就行了。

```cpp
/*
 * Author: ylx
 * Lang: c++
 * Prog: hdu6333
 */
const ll mod = 1e9 + 7;
const int maxn = 1e5 + 10; //320
int n, size, k[maxn], K;
ll ksm(ll x, int y)
{
    ll sum = 1;
    while (y)
    {
        if (y & 1)
            sum = sum * x % mod;
        x = x * x % mod;
        y >>= 1;
    }
    return sum;
}
ll jie[maxn], ny[maxn], Ans[maxn];
ll C(ll x, ll y)
{
    return jie[x] * ny[y] % mod * ny[x - y] % mod;
}
struct node
{
    int n, m, id;
} a[maxn];
bool cmp(node a, node b)
{
    if (k[a.n] == k[b.n])
        return a.m < b.m;
    return a.n < b.n;
}
int main()
{
#ifdef ylx
    freopen("hdu6333.in", "r", stdin);
    freopen("hdu6333.out", "w", stdout);
#endif
    n = 1e5;
    jie[0] = 1;
    for (int i = 1; i <= n; i++)
        jie[i] = jie[i - 1] * i % mod;
    ny[n] = ksm(jie[n], mod - 2);
    for (int i = n; i >= 1; i--)
        ny[i - 1] = ny[i] * i % mod;
    n = read();
    size = sqrt(n);
    for (int i = 1; i <= n; i++)
    {
        a[i].n = read(), a[i].m = read();
        a[i].id = i;
        k[i] = (i - 1) / size + 1;
    }
    K = k[n];
    sort(a + 1, a + n + 1, cmp);
    int l = 1, r = 1;
    ll ans = 2;
    ll Ny2 = ksm(2, mod - 2);
    for (int i = 1; i <= n; i++)
    {
        while (l < a[i].n) //往后移
        {
            ++l;
            ans = ans * 2 - C(l - 1, r);
            ans %= mod;
        }
        while (l > a[i].n)
        {
            ans = (ans + C(l - 1, r)) * Ny2 % mod;
            ans %= mod;
            --l;
        }
        while (r < a[i].m)
        {
            r++;
            ans += C(l, r);
            ans %= mod;
        }
        while (r > a[i].m)
        {
            ans -= C(l, r);
            ans %= mod;
            --r;
        }
        if (ans < 0)
            ans += mod;
        Ans[a[i].id] = ans;
    }
    for (int i = 1; i <= n; i++)
        printf("%lld\n", Ans[i]);
    return 0;
}
```

## HDU 6992 I love tree

### 题意

给你一个树，$1\le q,n\le 10^5$，有两个操作：

`1 a b` : for a chain <a, b>, increase the value of x2 to the x-th point on this chain
for example the chain from $a$ to $b=(x1,x2,x3,x4,x5)$, after the operation, $x1+=1,x2+=4,x3+=9,x4+=16,x5+=25$

`2 x` :asks the value of x-th node.

### 做法

对于一个连续的区间 $x,x+1, \dots , x+4$，给他们分别加上 $1,2^2, 3^2,4^2 ,5^2$，相当于对于每个位置分别加上 $(x+1-x)^2,[(x+1)+1-x]^2, \dots ,[(x+4)+1-x]^2$ 。

如果是加上 $5^2,4^2,3^2,2^2,1$ 呢？相当于给他们分别加上 $(-5)^2,(-4)^2,(-3)^2,(-2)^2,(-1)^2$，这样又是一个递增数列了。

```cpp
const int maxn = 1e5 + 100;
int n;
struct tree
{
    ll a[maxn << 2], b[maxn << 2], c[maxn << 2]; // a[d] 二次，b[d] 一次，c[d] 0次
    void bt(int x, int y, int d)
    {
        a[d] = b[d] = c[d] = 0;
        if (x == y)
            return;
        int mid = (x + y) >> 1;
        bt(x, mid, d << 1);
        bt(mid + 1, y, d << 1 | 1);
        return;
    }
    void pushdown(int x, int y, int d) //每个数加 fi^2,(fi+1)^2
    {
        if (x == y)
            return;
        a[d << 1] += a[d], a[d << 1 | 1] += a[d], a[d] = 0;
        b[d << 1] += b[d], b[d << 1 | 1] += b[d], b[d] = 0;
        c[d << 1] += c[d], c[d << 1 | 1] += c[d], c[d] = 0;
    }
    void xg(int x, int y, int d, int l, int r, ll fi) // fi:第一个数变成 (l+fi)^2
    {
        pushdown(x, y, d);
        if (l <= x && y <= r)
        {
            a[d]++;
            b[d] += 2 * fi;
            c[d] += fi * fi;
            return;
        }
        int mid = (x + y) >> 1;
        if (l <= mid)
            xg(x, mid, d << 1, l, r, fi);
        if (r > mid)
            xg(mid + 1, y, d << 1 | 1, l, r, fi);
    }
    ll query(int x, int y, int d, int p) //查询点p
    {
        pushdown(x, y, d);
        if (x == y)
        {
            return a[d] * x * x + b[d] * x + c[d];
        }
        int mid = (x + y) >> 1;
        if (p <= mid)
            return query(x, mid, d << 1, p);
        else
            return query(mid + 1, y, d << 1 | 1, p);
    }
} t;
int po[maxn], to[maxn << 1], ne[maxn << 1], id;
void add(int x, int y)
{
    id++;
    to[id] = y, ne[id] = po[x], po[x] = id;
}
int son[maxn], sz[maxn], fa[maxn], dep[maxn];
int top[maxn], dfn[maxn], cnt, Map[maxn];
void dfs1(int x)
{
    sz[x] = 1, son[x] = 0, dep[x] = dep[fa[x]] + 1;
    for (int i = po[x]; i; i = ne[i])
        if (to[i] != fa[x])
        {
            fa[to[i]] = x;
            dfs1(to[i]);
            sz[x] += sz[to[i]];
            if (sz[to[i]] > sz[son[x]])
                son[x] = to[i];
        }
}
void dfs2(int x, int lst)
{
    dfn[x] = ++cnt, Map[cnt] = x, top[x] = lst;
    if (son[x])
        dfs2(son[x], lst);
    else
        return;
    for (int i = po[x]; i; i = ne[i])
        if ((to[i] ^ fa[x]) && (to[i] ^ son[x]))
            dfs2(to[i], to[i]);
}
int lca(int x, int y)
{
    int tx = top[x], ty = top[y];
    while (tx != ty)
    {
        if (dep[ty] > dep[tx])
            swap(x, y), swap(tx, ty);
        x = fa[tx];
        tx = top[x];
    }
    if (dep[x] < dep[y])
        return x;
    return y;
}
int T;
void xg(int x, int y, int A) //x->y (A^2)...
{
    int Lca = lca(x, y);
    int tx = top[x], ty = top[y], f, L = 1, R = 1 + dep[x] + dep[y] - 2 * dep[Lca];
    while (tx != ty)
    {
        if (dep[ty] < dep[tx]) // x 往上跳
        {
            t.xg(1, n, 1, dfn[tx], dfn[x], -L - dfn[x]);
            L += dfn[x] - dfn[tx] + 1;
            x = fa[tx];
            tx = top[x];
        }
        else // y 往上跳
        {
            t.xg(1, n, 1, dfn[ty], dfn[y], R - dfn[y]);
            R -= dfn[y] - dfn[ty] + 1;
            y = fa[ty];
            ty = top[y];
        }
    }
    if (dfn[x] <= dfn[y])
    {
        t.xg(1, n, 1, dfn[x], dfn[y], L - dfn[x]);
    }
    else
    {
        t.xg(1, n, 1, dfn[y], dfn[x], -dfn[y] - R);
    }
}
int main()
{
#ifdef ylx
    freopen("hdu6962.in", "r", stdin);
    freopen("hdu6962.out", "w", stdout);
#endif
    n = read();
    for (int i = 1, x, y; i < n; i++)
    {
        x = read(), y = read();
        add(x, y);
        add(y, x);
    }
    dfs1(1);
    dfs2(1, 1); // 树剖
    T = read();
    int type, X, Y;
    while (T--)
    {
        type = read();
        if (type == 1)
        {
            X = read(), Y = read();
            xg(X, Y, 1); //i+x
        }
        else
        {
            X = read();
            ll ans = t.query(1, n, 1, dfn[X]);
            printf("%lld\n", ans);
        }
    }
    return 0;
}
```

## CF 1247

VP 了一场 Code forces Round #596 (Div. 2, based on Technocup 2020 Elimination Round 2)

[传送门](https://codeforces.com/contest/1247)

### C

**题意**

把一个数 $n(1\ le n \le 10^9,-1000\le p\le 1000)$ 表示成 $\sum_{i=1}^k (2^{b_i}-p)$，其中 $b_i\in \N$，最小化 $k$。

**做法**

可以发现，答案不会很大。然后就枚举答案，然后check。

对于一个数 $x$，可以用 $k$ 个 2 的次幂拼出来的条件是：在二进制下 $x$ 中 1 的个数小于等于 $k$，且 $k>x$。

```cpp
int n, p;
bool check(int x,int k)// x 二进制中 1 的个数是不是 k
{
    if (k > x)
        return false;
    if (x <= 0)
        return false;
    int sum = 0;
    for (int i = 0; i <= 30; i++)
        if (x & (1 << i))
        {
            sum++;
        }
    return sum <= k;
}
int main()
{
#ifdef ylx
    freopen("C.in", "r", stdin);
    freopen("C.out", "w", stdout);
#endif
    n = read(), p = read();
    for (int i = 1; i <= 100000; i++)
    {
        if (check(n - p * i, i))
        {
            cout << i;
            return 0;
        }
    }
    cout << -1;
    return 0;
}
```

### D

**题意**

给定 $n(2\le n \le 10^5)$ 个数，$1\le a_i\le 10^5$，求有多少对 $(i,j)$ 满足 $a_i\times a_j=x^k$。

**做法**

$k=2$ 时，把所有数质因数分解，把每个因子的次幂对 2 取模，设剩余的部分为 $x$，只有剩余部分相同时，才是合法的对。

$k>2$ 时，把所有满足 $t=x^k\le 10^{10}$ 的 $t$ 存起来。然后对于每个 $a_i$ 找是否有符合条件的 $a_j$ 以及个数。

```cpp
const int maxn = 1e5 + 10;
int n, k;
int a[maxn];
vector<pair<int, int>> b[maxn];
map<int, int> Map;
vector<ll> c;
int mp[maxn];
void work2()
{
    ll ans = 0;
    for (int i = 1, s; i <= n; i++)
    {
        s = 1;
        for (int j = b[i].size() - 1; j >= 0; j--)
        {
            b[i][j].second %= 2;
            if (b[i][j].second)
                s *= b[i][j].first;
        }
        ans += Map[s];
        Map[s]++;
    }
    cout << ans << endl;
}
int main()
{
#ifdef ylx
    freopen("D.in", "r", stdin);
    freopen("D.out", "w", stdout);
#endif
    n = read(), k = read();
    for (int i = 1, x, cnt; i <= n; i++)
        a[i] = read();
    if (k == 2)
    {
        for (int i = 1, x, cnt; i <= n; i++)
        {
            x = a[i];
            for (int j = 2; j * j <= x; j++)
                if (x % j == 0)
                {
                    cnt = 0;
                    while (x % j == 0)
                    {
                        x /= j;
                        cnt++;
                    }
                    b[i].push_back(make_pair(j, cnt));
                    if (x == 1)
                        break;
                }
            if (x > 1)
                b[i].push_back(make_pair(x, 1));
        }
        work2();
        return 0;
    }
    c.push_back(1);
    for (ll i = 2; i <= n; i++)
    {
        int flag = 0;
        ll ss = 1;
        for (int j = 1; j <= k; j++)
        {
            ss *= i;
            if (ss > 1e10)
            {
                flag = 1;
                break;
            }
        }
        if (flag)
            break;
        c.push_back(ss);
    }
    ll ans = 0;
    for (int i = 1; i <= n; i++)
    {
        for (auto x : c)
        {
            if (x % a[i] == 0 && x / a[i] <= 100000)
                ans += mp[x / a[i]];
        }
        mp[a[i]]++;
    }
    printf("%lld\n", ans);
    return 0;
}
```

## CF 1548

### B

**题意**

给定 $n$ 和一个长度为 $n$ 的数组 $a$，求一个**最长**的区间 $\left[l,r\right]$，使得存在 $m\geq 2$ 和 $k$，对于所有 $l\leq i\leq r,a_i\equiv k\pmod{m}$（即**区间内所有数对 $m$ 取模余数相等**），输出最长区间长度（区间长度定义为 $r-l+1$）。

**做法**

考虑求出数组 $a$ 的差分数组，数组 $b$ 。然后问题转化为，找出最长的一段 $[l,r]$ 满足 $\gcd\limits _{i=l+1}^r b_i\ge 2$，注意本题需要使用 long long，以及有一点点小细节。

**代码**

```cpp
int T;
const int maxn = 2e5 + 100;
ll a[maxn], b[maxn], n;
ll g[18][maxn], Log[maxn];
void init()
{
    for (int i = 2; i <= n; i++)
    {
        g[0][i] = b[i];
    }
    for (int j = 1; j <= 17; j++)
    {
        for (int i = 2; i + (1 << j) - 1 <= n; i++)
        {
            g[j][i] = __gcd(g[j - 1][i], g[j - 1][i + (1 << (j - 1))]);
        }
    }
}
ll qgcd(int x, int y) //(l,x) (l,y-(1<<l)+1)
{
    int l = Log[y - x + 1];
    return __gcd(g[l][x], g[l][y - (1 << l) + 1]);
}
int main()
{
#ifdef ylx
    freopen("1548B.in", "r", stdin);
    freopen("1548B.out", "w", stdout);
#endif
    T = read();
    Log[1] = 0;
    for (int i = 2; i <= 200000; i++)
        Log[i] = Log[i / 2] + 1;
    while (T--)
    {
        n = read();
        int cnt = 0, Max = 0;
        for (int i = 1; i <= n; i++)
        {
            a[i] = read();
            b[i] = abs(a[i] - a[i - 1]);
            if (b[i] == 0 && i != 1)
                cnt++;
            else
                cnt = 0;
            Max = max(Max, cnt);
        }
        Max++;
        if (n == 1)
        {
            printf("1\n");
            continue;
        }
        int ans = max(1, Max);
        init();
        for (int i = 2; i <= n; i++)
        {
            int l = i, r = n;
            while (l <= r)
            {
                int mid = (l + r) >> 1;
                ll G = qgcd(i, mid);
                if (G >= 2 || G == 0)
                {
                    ans = max(ans, mid - i + 2);
                    l = mid + 1;
                }
                else
                    r = mid - 1;
            }
        }
        if (ans == 1)
        {
            for (int i = 1; i < n; i++)
                if ((b[i + 1] == 0 && a[i] != 1) || (b[i + 1] > 1))
                {
                    ans = 2;
                    break;
                }
        }
        for (int i = 1; i <= n; i++)
            a[i] = 0, g[0][i] = 0;
        printf("%d\n", ans);
    }
    return 0;
}
```

## ARC 127

好久没有写过 ARC，不太熟悉风格，打得很不好。

### C Binary Strings

**题意**

给你一个 $n(n\le 10^6)$，求 $1$ 到 $2^{n-1}$ 按照字典序排列后，第 $X$ 个数是多少。

$X$ 为一个长度不超过 $n$ 的二进制数。 

**做法**

开始一直在数量上面统计，没有直接去观察数列每一位情况，实属失策。

以 $n=4$ 为例，

n = 3 时候的完整数列：`1 10 100 101 11 110 111`

n = 4 时候的完整数列：`1 10 100 1000 1001 101 1010 1011 11 110 1100 1101 111 1110 1111 `

第一位一定是 1，然后我们删去第一位来观察：
```
_ 
0 00 000 001 01 010 011
1 10 100 101 11 110 111 
```

第 1 个是空，这时候什么都不输出，直接break。
第 $2\to8$ 个第一位是 0，后面的是 1。

后面的部分与 $n-1$ 时相同，这时候，如果 $x\le 2^{n-1}$ 则将 $x$ 减去 1。否则将 $x$ 减去 $2^{n-1}$

**代码**

```cpp
/*
 * Author: ylx
 * Lang: c++
 * Prog: C
 */
const int maxn = 1e6 + 10;
int c[maxn];
int n, l, cnt1;
string s;
void dec() // -1
{
    int i;
    for (i = n; i >= 1; i--)
        if (c[i] == 0)
            c[i] = 1, cnt1++;
        else
            break;
    c[i] = 0, cnt1--;
}
void inc()
{
    int i;
    for (i = n; i >= 1; i--)
        if (c[i] == 1)
            c[i] = 0, cnt1--;
        else
            break;
    c[i] = 1, cnt1++;
}
int main()
{
#ifdef ylx
    freopen("C.in", "r", stdin);
    freopen("C.out", "w", stdout);
#endif
    n = read();
    cin >> s;
    l = s.length();
    for (int i = n, j = l - 1; j >= 0; i--, j--)
    {
        c[i] = s[j] - '0';
        if (c[i])
            cnt1++;
    }
    for (int i = n - l; i >= 1; i--)
        c[i] = 0;
    putchar('1');
    for (int i = 1; i <= n; i++)
    {
        if (c[n] == 1 && cnt1 == 1)
            break;
        dec();
        if (c[i] == 0)
            putchar('0');
        else
        {
            putchar('1');
            c[i] = 0;
            cnt1--;
            inc();
        }
    }
    return 0;
}
```

### D Sum of Min of Xor

**题意**

Given are sequences of $N$ integers each: $(A_1,A_2,\dots A_N)$ and $(B_1,B_2,\dots, B_N)$.

Find $\sum\limits 1\le i<j\le N \min(A_i⊕A_j,B_i⊕B_j)$, where $⊕$ denotes the bitwise XOR.

Constraints：$1\le N\le 2.5\times10^5,0\le A_i,B_i<2^{18}$

**做法**

设 $c_i=a_i ⊕b_i$，然后建立一棵 01trie。

考虑什么时候知道最终贡献：

某一个 $c_i=0,c_j=1$ 的时候，可以知道贡献是来自于 $a$ 还是 $b$。
设一对数为 $a,b$。 

1. $(0,1),(0,0)$  这个时候答案来自 $a_i$

2. $(1,0),(0,0)$  这个时候答案来自 $b_i$

考虑答案来自 $a_i$ 的情况，某一位产生贡献满足的条件是：$a_i$ 与 $a_j$ 的这一位不同。

具体操作：每次先对 $i$ 查询答案，再将 $i$ 插入到 trie 中。

插入过程，需要在经过的每一个点记录四个信息：$a,b$ 的每一位为 0/1 的情况，$a,b$ 当前位 0/1 的情况（为了知道最终答案是来自于 $a$ 还是 $b$）

查询过程：设用点对 $(A,B)$ 进行查询，每次走到与 $A\^\ B$ 当前位相同的子树，另一半直接求解答案。

**代码**

```cpp
/*
 * Author: ylx
 * Lang: c++
 * Prog: D
 */
const int maxn = 250000 + 10;
int n;
int a[maxn], b[maxn], c[maxn];
ll cnt[4]; //  a&b,10,a|b,01
namespace trie
{
    int n;
    struct node
    {
        int flag = 0;
        int a[18][2][2], b[18][2][2]; // 记录每一位 0 和 1 的个数
        void add(int x, int y, int p) // 将某对 (a，b) 信息记录到这个节点。
        {
            flag = 1;
            for (int i = 0; i < 18; i++)
            {
                a[i][(x >> i) & 1][(x >> p) & 1]++;
                b[i][(y >> i) & 1][(y >> p) & 1]++;
            }
            return;
        }
        ll find(int A, int B, int p) // 查询某一对 (a, b) 在该节点的答案。
        {
            ll sum = 0;
            for (int i = 0; i < 18; i++)
            {
                sum += (1ll << i) * a[i][((A >> i) & 1) ^ 1][(A >> p) & 1];
                sum += (1ll << i) * b[i][((B >> i) & 1) ^ 1][(B >> p) & 1];
            }
            return sum;
        }
        ll qans(int A) // (ai xor aj) 与 (bi xor bj) 相同时候的答案
        {
            ll sum = 0;
            for (int i = 0; i < 18; i++)
                for (int j = 0; j <= 1; j++)
                {
                    sum += (1ll << i) * a[i][((A >> i) & 1) ^ 1][j];
                }
            return sum;
        }
    } c[1000010];
    void insert(int C, int A, int B)
    {
        int now = 1;
        for (int i = 17; i >= 0; i--) // 从高位到低位进行处理
        {
            now = (now << 1) | ((C >> i) & 1);
            c[now].add(A, B, i);
        }
    }
    ll ans = 0;
    void find(int C, int A, int B)
    {
        int now = 1;
        for (int i = 17; i >= 0; i--)
        {
            now = (now << 1) | ((C >> i) & 1);
            ans += c[now ^ 1].find(A, B, i);
        }
        ans += c[now].qans(A);
    }
}
int main()
{
#ifdef ylx
    freopen("D.in", "r", stdin);
    freopen("D.out", "w", stdout);
#endif
    n = read();
    for (int i = 1; i <= n; i++)
        a[i] = read();
    for (int i = 1; i <= n; i++)
        b[i] = read();
    for (int i = 1; i <= n; i++)
    {
        c[i] = a[i] ^ b[i];
        if (i > 1)
            trie::find(c[i], a[i], b[i]);
        trie::insert(c[i], a[i], b[i]);
    }
    printf("%lld\n", trie::ans);
    return 0;
}
```

### E Priority Queue

**题意**

$A+B$ 次操作：$s$ is a set. 

操作 1：Choose an integer $v$ such that $1≤v≤A$ and add it to s. Here, $v$ must not be an integer that was chosen in a previous operation.

操作 2：Delete from $s$ the element with the largest value. The input guarantees that $s$ is not empty just before this operation.

How many sets are there that can be the final state of $s$? Find the count modulo $998244353$.

Constraints：$1≤A≤5000$，$0≤B≤A−1$，进行操作 2 时保证 $s$ 不为空。

**做法**

考虑某一个数插入之后最终留在了集合中：插入之后，每次删除操作的时候，总有一个比自己大的数。

使最终的数列字典序最大：每次插入当前没插入的数中最大的，这样小数被删除的概率最大 。（易证）

考虑比这个字典序小的（这里要求每一位都更小），一定可以构造出，因为遇到操作 2 时可以找到一个更大的不是答案的数。（考虑通过字典序最大的情况交换插入顺序，一定是把更大的数字放到前面，让它被删掉，从而更小的数字可以被保留）

$f_{i,j,0/1}$ 表示插入到了第 $i$ 个数，当前数是 $j$ 的方案数，当前(是/否)为字典序最大情况。

转移是一个前缀和转移。设字典序最大的时候第 $i$ 位为 $b_i$

$f_{i,b_{i},1}=1$，余下的 $f_{i,j,1}$ 为 0。

$$
f_{i,j,0}=[j\le b_i]\times \sum_{k=1} ^{j-1} f_{i-1,k,0}+[j>b_{i-1} \& j< b_i]\times f_{i-1,b_{i-1},1}
$$

这种题的思考方向：考虑什么样的最终序列合法，先考虑边界情况（最大或者最小），再考虑可以通过变换得到什么样的序列。

```cpp
/*
 * Author: ylx
 * Lang: c++
 * Prog: D
 */ 
const ll mod = 998244353;
const int maxn = 5e3 + 10;
int A, B, n, a[maxn + maxn], b[maxn];
ll f[maxn][maxn];
ll g[maxn][maxn];
int gap[maxn];
void Mod(ll &x)
{
    if (x >= mod)
        x -= mod;
}
int main()
{
#ifdef ylx
    freopen("E.in", "r", stdin);
    freopen("E.out", "w", stdout);
#endif
    A = read(), B = read();
    int id = 0;
    for (int i = 1; i <= A + B; i++)
    {
        a[i] = read();
        if (a[i] == 1)
            b[++n] = ++id;
        else
            --n;
    }
    /*
    for (int i = 1; i <= n; i++)
        cout << b[i] << " ";
    cout << endl;
    */
    for (int i = 1; i <= n; i++)
    {
        for (int j = 1; j <= A; j++)
        {
            if (j <= b[i])
            {
                f[i][j] = g[i - 1][j - 1];
                if (j > b[i - 1] && j < b[i])
                    f[i][j]++;
                Mod(f[i][j]);
            }
            g[i][j] = g[i][j - 1] + f[i][j];
            Mod(g[i][j]);
        }
    }
    ll ans = 1;
    for (int i = 1; i <= A; i++)
    {
        ans += f[n][i];
        Mod(ans);
    }
    printf("%lld\n", ans);
    return 0;
}
```


## ICPC 网络赛 Round 2

### K

题意略。第一眼就看出来是个状态压缩，然而 $n^2\times2^n$ 是过不去 20 的，所以需要卡一下有效状态。不过并没有被卡常。

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
ll dp[(1 << 20) + 2];
ll P[(1 << 20) + 2];
ll a[21][21];
ll ans[21][21];
const int mod = 998244353;
ll quick(ll x, ll y)
{
	ll res = 1;
	while (y)
	{
		if (y & 1)
			res = res * x % mod;
		x = x * x % mod;
		y >>= 1;
	}
	return res;
}
int main()
{
	int n;
	scanf("%d", &n);
	for (int i = 0; i < n; ++i)
	{
		for (int j = 0; j < n; ++j)
		{
			scanf("%lld", &a[i][j]);
		}
	}
	for (int i = 0; i < (1 << n); ++i) // 统计概率和
	{
		int cc = __builtin_popcount(i); 
		ll sum = 0;
		for (int j = 0; j < n; ++j)
		{
			if (i >> j & 1)
				sum += a[n - cc][j];
		}
		P[i] = quick(sum, mod - 2);
	}
	dp[0] = 1ll;
	for (int i = 1; i < (1 << n); ++i)
	{
		int cc = __builtin_popcount(i); 
		cc--;
		for (int j = 0; j < n; j++)
			if (i & (1 << j))
			{
				int S = (i ^ (1 << j));
				S = (1 << n) - S - 1;
				ll tmp = (ll)dp[i ^ (1 << j)] * P[S] % mod * a[cc][j] % mod;
				dp[i] = (dp[i] + tmp) % mod;
				ans[cc][j] = (ans[cc][j] + tmp) % mod;
			}
	}
	for (int i = 0; i < n; ++i)
	{
		for (int j = 0; j < n; ++j)
		{
			printf("%lld", ans[i][j]);
			if (j != n - 1)
				printf(" ");
		}
		if (i != n - 1)
			printf("\n");
	}
}
```

### L

**题意**

求 $\sum\limits _{i=l} ^r \phi(a_i)$，区间修改操作为对于区间 $[l,r]$ 每个数乘上一个 $w_i$。

数据范围：$1\le a_i,w_i\le 100,n,q\le2\times10^5$。

**做法**

orz lkl 给了一堆和 $\phi(x)$ 有关的式子。

开始想维护 $gcd$ 合并，但是线段树难以处理每个位置加不同的数还要求和的情况。

所以使用 $\phi(x)=x\times\prod\limits_{i=1}^{|p|}\frac{p_i-1}{p_i}$，其中，$x=\prod\limits_{i=1}^{|p|}p_i^{x_i}$，也就是 $x$ 质因数分解后的结果。

然后每次出现全新的 $p_i$ 的时候传下去单点暴力修改即可（每个点修改次数不会超过24），状压一下记录是否出现。

以及维护一个区间乘。

**代码**

```cpp
#include <bits/stdc++.h>
#define ll long long
using namespace std;
const int maxn = 2e5 + 10;
const ll mod = 998244353;
const int Prm[30] = {2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97};
ll ny[30];
ll ksm(ll x, int y)
{
    ll s = 1;
    while (y)
    {
        if (y & 1)
            s = s * x % mod;
        x = x * x % mod;
        y >>= 1;
    }
    return s;
}
int check(int x)
{
    int s = 0;
    for (int i = 0; i < 25; i++)
        if (x % Prm[i] == 0)
            s |= (1 << i);
    return s;
}
int a[maxn];
struct tree
{
    int t[maxn * 4]; //质因子是否出现
    ll s[maxn * 4];  //用于存答案
    ll lazy[maxn * 4];
    void bt(int x, int y, int d)
    {
        lazy[d] = 1;
        if (x == y)
        {
            s[d] = a[x];
            for (int i = 0; i < 25; i++)
                if (a[x] % Prm[i] == 0)
                {
                    s[d] = s[d] * (Prm[i] - 1) % mod * ny[i] % mod;
                    t[d] |= (1 << i);
                }
            return;
        }
        int mid = (x + y) >> 1;
        bt(x, mid, d << 1);
        bt(mid + 1, y, d << 1 | 1);
        t[d] = t[d << 1] & t[d << 1 | 1];
        s[d] = (s[d << 1] + s[d << 1 | 1]) % mod;
    }
    void pushdown(int d)
    {
        if (lazy[d] != 1)
        {
            int D = lazy[d];
            (s[d << 1] *= D) %= mod;
            (s[d << 1 | 1] *= D) %= mod;
            (lazy[d << 1] *= D) %= mod;
            (lazy[d << 1 | 1] *= D) %= mod;
            lazy[d] = 1;
        }
    }
    void change1(int x, int y, int d, int l, int r, int ww) //修改
    {
        pushdown(d);
        if (l <= x && y <= r)
        {
            s[d] *= ww;
            s[d] %= mod;
            lazy[d] *= ww;
            lazy[d] %= mod;
            return;
        }
        int mid = (x + y) >> 1;
        if (l <= mid)
            change1(x, mid, d << 1, l, r, ww);
        if (r > mid)
            change1(mid + 1, y, d << 1 | 1, l, r, ww);
        s[d] = s[d << 1] + s[d << 1 | 1];
        s[d] %= mod;
    }
    void change2(int x, int y, int d, int l, int r, int w)
    {
        pushdown(d);
        if ((t[d] & w) == w)
            return;
        if (x == y)
        {
            for (int i = 0; i < 25; i++)
                if ((w & (1 << i)) && (!(t[d] & (1 << i))))
                {
                    s[d] = s[d] * (Prm[i] - 1) % mod * ny[i] % mod;
                    t[d] |= (1 << i);
                }
            return;
        }
        int mid = (x + y) >> 1;
        if (l <= mid)
            change2(x, mid, d << 1, l, r, w);
        if (r > mid)
            change2(mid + 1, y, d << 1 | 1, l, r, w);
        s[d] = (s[d << 1] + s[d << 1 | 1]) % mod;
        t[d] = t[d << 1] & t[d << 1 | 1];
    }
    ll query(int x, int y, int d, int l, int r)
    {
        pushdown(d);
        if (l <= x && y <= r)
        {
            return s[d];
        }
        int mid = (x + y) >> 1;
        ll sum = 0;
        if (l <= mid)
            sum += query(x, mid, d << 1, l, r);
        if (r > mid)
            sum += query(mid + 1, y, d << 1 | 1, l, r);
        sum %= mod;
        return sum;
    }
} T;
int n, TT;
void init()
{
    for (int i = 0; i < 25; i++)
    {
        ny[i] = ksm(Prm[i], mod - 2);
    }
    scanf("%d%d", &n, &TT);
    for (int i = 1; i <= n; i++)
        scanf("%d", &a[i]);
    T.bt(1, n, 1);
}
int ans[maxn], idans;
int main()
{
    init();
    int type, x, y, w;
    while (TT--)
    {
        scanf("%d%d%d", &type, &x, &y);
        if (type == 0)
            scanf("%d", &w);
        if (type == 1)
        {
            ++idans;
            ans[idans] = T.query(1, n, 1, x, y);
            if (ans[idans] < 0)
                ans[idans] += mod;
        }
        else
        {
            T.change1(1, n, 1, x, y, w);
            int qwq = check(w);
            T.change2(1, n, 1, x, y, qwq);
        }
    }
    for (int i = 1; i < idans; i++)
        printf("%lld\n", ans[i]);
    printf("%lld", ans[idans]);
    return 0;
}
```

## 训练方向

得训练一些没有太多人可以通过的图论，数据结构题了。

好像计算几何也是我的。

