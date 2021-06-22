---

title: GYM 101669 乱做
slug: gym101669
date: 2019-04-21 13:00:00 UTC+08:00
tags: dp, 数据结构, 并查集, 贪心, 构造, 博弈, 线段树, dfs序
category: OI
author: yilanxuan
has_math: true

---

两个人和一只兔子一起开的vp。

<!-- TEASER_END:  -->

gym 101669 2017-2018 ACM-ICPC Southeastern European Regional Programming Contest (SEERC 2017) 乱做

tag : dp, 

AG签到，CEHI没补。~~也不太可做。~~

L 留坑明天填。

A实际范围与题面范围不一样。

## B

### 题意

你有 $n$ 个盒子，$m$ 次操作，初始情况下 $n$ 个盒子都是空的。

每次操作输入一个 $x$，表示在位置 $x$ 上方扔一个球下来。

1. 若位置 $x$ 为空，则直接落入位置 $x$ 上。
2. 否则随机往左边或者右边滚，直到到一个空的位置上。

### 做法

$f_i$ 表示目前有 $i$ 个空位置时候的方案数。

设当前在位置 $i$ ，开个 $cnt$ 记空位置数。若 $cnt<0$，则代表目前的 $i$ 个球不够放/恰好放。

转移的话，就是两种情况，第一种是这个位置有球（即 $f_{cnt}$），第二种是这个位置是空的（即 $f_{cnt-1}$）。

答案就是 $f_{cnt}$

~~具体看代码。~~

### 代码

```cpp
#include <bits/stdc++.h>
using namespace std;
const int mod = 1e9 + 7;
int n, m, cnt, f[1000005], sum[1000005];
void add(int &x, int val)
{
    x += val;
    if (x >= mod)
        x -= mod;
}
int main()
{
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= m; i++)
    {
        int x;
        scanf("%d", &x);
        sum[x]++;
    }
    f[0] = 1;
    for (int i = 1; i <= n; i++)
    {
        cnt++;
        if (sum[i])
            cnt -= sum[i];
        else if (cnt > 0)
            add(f[cnt], f[cnt - 1]);
    }
    printf("%d\n", f[cnt]);
    return 0;
}
```

## D

### 题意

给出 $n$ 个向量，每个向量有 $m$ 维，$n$ 个向量中有且只有两维上是一，然后重载了加号运算符和乘号运算符，求最多的非线性相关的向量个数。

### 做法

开始前置技能不足+看漏条件。

因为每个向量中有且只有两个1，那么可以想象答案最大肯定是 $m-1$ 因为分布肯定是这样的。（互不影响的要加起来）

![](https://images2018.cnblogs.com/blog/1459194/201808/1459194-20180818213040095-1120001774.png)


### 代码

```cpp
const int maxn = 1e5 + 10;
int fa[maxn], b[maxn];
int n, m, sz[maxn], ans;
int gf(int x)
{
    if (fa[x] == x)
        return x;
    return fa[x] = gf(fa[x]);
}
int main()
{
    n = read(), m = read();
    for (int i = 1; i <= n; i++)
        fa[i] = i;
    for (int i = 1, k, x; i <= n; i++)
    {
        k = read();
        while (k--)
        {
            x = read();
            if (!b[x])
                b[x] = i;
            else
            {
                if (gf(b[x]) != gf(i))
                    fa[gf(b[x])] = gf(i);
            }
        }
    }
    for (int i = 1; i <= n; i++)
        sz[gf(i)]++;
    for (int i = 1; i <= n; i++)
        ans += max(0, sz[i] - 1);
    cout << ans << endl;
    return 0;
}
```

## F

### 题意

给出两个`01`串，每位有一个权值，然后要从$a$串变成$b$串，每换一次的花费是**变之后** `a`串中里面每一位是$1$的对应的权值和，求最小花费。

$n\le 5\times 10^3 ,1\le c_i \le 10^9$

### 做法

先把 `1` 变成 `0` ，先变权值大的位置。

然后再把 `0` 变成 `1`，先变权值较小的位置。

然而你可以先把一些 `1` 变成 `0` 再变回来可能会更优。这些一定是权值最大的若干个（统一种变化类型中的）。

$n\le 5\times 10^3$，所以我们可以枚举变化多少个，然后和扔到另外两种情况里面考虑。（和那些一起sort，然后考虑顺序）

### 代码

cxr写的。

```cpp
typedef long long ll;
const int N = 5005;
int n;
ll ans = (ll)1e18, sum;
int w[N];
char a[N], b[N];
vector<int> p;
multiset<ll> s0, s1;
multiset<ll>::iterator it1;
multiset<ll>::reverse_iterator it2;
inline void solve()
{
    ll res = 0, s = sum;
    for (it2 = s0.rbegin(); it2 != s0.rend(); ++it2)
        s -= (*it2), res += s;
    for (it1 = s1.begin(); it1 != s1.end(); ++it1)
        s += (*it1), res += s;
    ans = min(ans, res);
}
int main()
{
    n = read();
    for (int i = 1; i <= n; ++i)
        w[i] = read();
    scanf("%s", a + 1);
    scanf("%s", b + 1);
    for (int i = 1; i <= n; ++i)
    {
        if (a[i] == '1')
        {
            sum += w[i];
            if (a[i] == b[i])
                p.push_back(w[i]);
            else
                s0.insert(w[i]);
        }
        else if (b[i] == '1')
            s1.insert(w[i]);
    }
    sort(p.begin(), p.end());
    reverse(p.begin(), p.end());
    solve();
    for (int i = 0, sz = p.size(); i < sz; ++i)
    {
        s0.insert(p[i]), s1.insert(p[i]);
        solve();
    }
    printf("%lld\n", ans);
    return 0;
}
```

## J

### 题意

有 $n$ 堆石头，第 $i$ 堆有 $a_i$ 个。

A先取，每次从一堆中取的数量不等于 $0$，然后B和C取，无法操作者输。

B和C想让A输，假设每个人的决策都是最佳的，求A是必败还是必输。

$n\le 10^5$

### 做法

考虑多种情况。

全是1的情况。

每次每个人取走一堆，当$n\mod 3=0$ 的时候A会输，否则胜。

只有一个不是1的情况。

可以根据 $n\mod 3$ 来让A决定，是将不为1的一堆变成1，还是直接删掉这一堆，所以A必胜。

有两个不是1的情况。

如果留了一个石子个数 $>2$ 的堆，那么后手两个人可以同时控制这堆石子，所以肯定是给后手留一个大小为2石子。但是如果 $n\mod 3=2$ 的情况下，后手可以通过是取走一堆石子还是两堆石子来使先手必败。 

其他情况后手一定可以通过一些操作让先手必败。

### 代码

```cpp
typedef long long ll;
const int N = 1e5 + 5;
int n, p, q;
int main()
{
    n = read();
    for (int i = 1; i <= n; ++i)
    {
        int x = read();
        if (x == 1)
            ++p;
        else if (x == 2)
            ++q;
    }
    if (p == n)
    {
        if (n % 3)
            puts("Win");
        else
            puts("Lose");
        return 0;
    }
    if (p + 1 == n)
    {
        puts("Win");
        return 0;
    }
    if (p + 2 == n)
    {
        if (q && n % 3 != 2)
            puts("Win");
        else
            puts("Lose");
        return 0;
    }
    puts("Lose");
    return 0;
}
```

## K

### 题意

有一个长度为 $n$ 的序列，里面的数是 $1-n$ 。

然后给出每一位以当前位为队头的最长上升子序列长度$a_i$，还原原序列，多个答案输出字典序最小的那个。

$n\le 10^5$。

### 做法

我们按照 $a_i$ 分层，设每个位置权值为 $b_i$ 。

因为题目保证有解，所以有一种合法的方案，就是 $a_i$ 小的放尽量大的数 。

多个位置 $a_i$ 相同时，位置靠前的分配较大的 。

这样一定是正确的。

1. 对于$a_i>1$，一定可以找到位置 $j>i$ ，满足 $a_j=a_i-1$ 且 $b_j>b_i$ 。
2. 其他的合法方案，若要将 $b_i$ 变小，则必须考虑一个位置 $j< i$ 将 $b_j$ 变大，这样一定不优。
3. 若 $a_i=1$，则后面不能出现比它大的数，则要么变前面的数，要么不存在这样的操作，所以会更劣。

~~证明随便看看就好。~~

### 代码

```cpp
const int maxn = 1e5 + 10;
int n, a[maxn];
int cnt[maxn],Max;
int main()
{
    n = read();
    for (int i = 1; i <= n; i++)
    {
        a[i] = read();
        cnt[a[i]]++;
        qmax(Max, a[i]);
    }
    for (int i = Max - 1; i >= 1; i--)
        cnt[i] += cnt[i + 1];
    for (int i = 1; i <= n; i++)
        printf("%d ", cnt[a[i]]--);
    return 0;
}
```

## L

### 题意

给你两棵树构成的图，问

1.至少要删去多少条边使其变成至少两个联通块。
2.求方案数。

$n\le 10^5$

### 做法

可以发现第一问答案为 2 或者 3 。

证：因为需要在两棵树上都断开，所以至少为 2 。

若答案为 4，则需要有边 $\frac{4n}{2}=2n$ 条。

所以只有两种情况。

一定是在一棵树删一条边，另一棵树上删1～2条边。

大致思路：枚举在一棵树上断开哪条边，将这棵树分成两个联通块，看在另一颗树上这两个联通块之间有多少条边。

具体操作：相当于dfs一棵树，另外一棵树当作标记挂在每个端点和LCA处，两个端点处 `+1`，LCA 处 `-2`。

每次判断一个点和其父亲的连边是否可以删除。

若一棵子树的权值和+1=第一问的答案，则答案+1（根节点因为没有父亲不能算）

注意：答案为2的时候只需要算一次。

dfs序 + 线段树即可。

### 代码

暂时性的咕咕咕了。
