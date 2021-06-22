---

title: NOI2015 补题
slug: NOI2015
date: 2019-06-05 13:00:00 UTC+08:00
tags: SA, 并查集, 哈夫曼树, 字符串, SAM, dp, 状压, 数论
category: OI
author: yilanxuan
has_math: true

---

完成度：4/6

<!-- TEASER_END:  -->

[传送门](https://www.luogu.org/fe/problem/list?keyword=NOI2015&orderBy=pid&order=asc&tag=77%7C32&page=1)

## 程序自动分析

出题人翻车系列。

```cpp
const int maxn = 2e5 + 10;
int T;
int n;
struct node
{
    int ty, x, y;
} b[maxn];
int a[maxn], cnt, fa[maxn];
map<int, int> Map;
int gf(int x)
{
    if (fa[x] == x)
        return x;
    return fa[x] = gf(fa[x]);
}
int main()
{
#ifdef ylx
    freopen("p1955.in", "r", stdin);
    freopen("p1955.out", "w", stdout);
#endif
    T = read();
    while (T--)
    {
        int flag = 1;
        Map.clear();
        n = read();
        cnt = 0;
        for (int i = 1; i <= n; i++)
        {
            b[i].x = read(), b[i].y = read(), b[i].ty = read();
            cnt++;
            a[cnt] = b[i].x;
            cnt++;
            a[cnt] = b[i].y;
        }
        sort(a + 1, a + cnt + 1);
        cnt = unique(a + 1, a + cnt + 1) - a - 1;
        for (int i = 1; i <= cnt; i++)
        {
            fa[i] = i;
            Map[a[i]] = i;
        }
        for (int i = 1; i <= n; i++)
            if (b[i].ty == 1)
            {
                b[i].x = Map[b[i].x];
                b[i].y = Map[b[i].y];
                fa[gf(b[i].x)] = gf(b[i].y);
            }
        for (int i = 1; i <= n; i++)
            if (b[i].ty == 0)
            {
                b[i].x = Map[b[i].x];
                b[i].y = Map[b[i].y];
                if (gf(b[i].x) == gf(b[i].y))
                {
                    flag = 0;
                    printf("NO\n");
                    break;
                }
            }
        if (flag)
        {
            printf("YES\n");
        }
    }
}
```

## 软件包管理器

暂时没写。

## 寿司晚宴

果真又是这种复杂度和根号有关系的题（不超过 $1$ 个质因数$>22$）

$dp\_{i,j,k}$ 表示处理到素数$i$，小G的状态的集合为 $j$ ，小M的状态集合为 $k$ 的方案数。$ 95\times 2^{16}$ 种状态。

根据每个数所含的最大的质因子从小到大转移。

开始写的一起转移（同一种状态一起转移），调不出，然后看的sol，发现一个一个转移比一起转移好写多了。

```cpp
const int maxn = 500 + 10;
const int maxm = 8;
const int N = 500;
const int pr[8] = {2, 3, 5, 7, 11, 13, 17, 19};
int n;
ll p;
struct node
{
    int x, big, y; //y:小素数集合
    void init()
    {
        big = 0;
        int z = x;
        for (int i = 0; i < 8; i++)
            if (z % pr[i] == 0)
            {
                while (z % pr[i] == 0)
                    z /= pr[i];
                y |= (1 << i);
                big = pr[i];
            }
        if (z != 1)
            big = z;
    }
} a[maxn];
bool cmp(node A, node B)
{
    return A.big < B.big;
}
int m;
ll dp[1 << 8][1 << 8], f[1 << 8][1 << 8], g[1 << 8][1 << 8];
void Mod(ll &x)
{
    if (x >= p)
        x -= p;
    if (x < 0)
        x += p;
}
int main()
{
#ifdef ylx
    freopen("p2150.in", "r", stdin);
    freopen("p2150.out", "w", stdout);
#endif
    n = read(), p = read();
    for (int i = 1; i < n; i++)
    {
        a[i].x = i + 1;
        a[i].init();
    }
    sort(a + 1, a + n, cmp);
    dp[0][0] = 1;
    m = (1 << 8) - 1;
    for (int i = 1, S; i < n; i++)
    {
        if (i == 1 || a[i].big != a[i - 1].big)
        {
            memcpy(f, dp, sizeof(f));
            memcpy(g, dp, sizeof(g));
        }
        S = a[i].y;
        for (int j = m, J; j >= 0; j--)
        {
            J = m - j;
            for (int k = J; k >= 0; k = (k - 1) & J)
            {
                if (!(k & S))
                    Mod(f[j | S][k] += f[j][k]);
                if (!(j & S))
                    Mod(g[j][k | S] += g[j][k]);
                if (!k)
                    break;
            }
        }
        if (i == n - 1 || a[i].big != a[i + 1].big)
        {
            for (int j = m, J; j >= 0; j--)
            {
                J = m - j;
                for (int k = J; k >= 0; k = (k - 1) & J)
                {
                    dp[j][k] = (f[j][k] + g[j][k] - dp[j][k] + p) % p;
                    if (!k)
                        break;
                }
            }
        }
    }
    ll ans = 0;
    for (int j = m, J; j >= 0; j--)
    {
        J = m - j;
        for (int k = J; k >= 0; k = (k - 1) & J)
        {
            Mod(ans += dp[j][k]);
            if (!k)
                break;
        }
    }
    write(ans);
    return 0;
}
```

## 荷马史诗

哈夫曼树，开始想从上往下分的做法，然后发现不好处理。（要保证复杂度的话不好处理）。

于是写了一个合并的做法，大致思路：每次将最小的 $k$ 个权值合并。

```cpp
const int maxn = 1e5 + 100;
ll w[maxn];
int n, k;
struct node
{
    ll dep, w;
    bool operator<(const node &A) const
    {
        if (w == A.w)
            return dep > A.dep;
        return w > A.w;
    }
};
priority_queue<node> q;
int main()
{
#ifdef ylx
    freopen("p2168.in", "r", stdin);
    freopen("p2168.out", "w", stdout);
#endif
    n = read(), k = read();
    for (int i = 1; i <= n; i++)
        w[i] = read();
    int m = k - 1 - (n - k) % (k - 1);
    m %= (k - 1);
    n += m;
    sort(w + 1, w + n + 1);
    for (int i = 1; i <= n; i++)
    {
        q.push((node){1, w[i]});
    }
    ll ans = 0, s = 0, md = 0, dp = 0;
    node x;
    while (!q.empty())
    {
        s = 0;
        dp = 0;
        for (int i = 1; i <= k; i++)
        {
            x = q.top();
            q.pop();
            s += x.w;
            dp = max(dp, x.dep);
        }
        ans += s;
        if (q.empty())
        {
            md = dp;
            break;
        }
        q.push((node){dp + 1, s});
    }
    printf("%lld\n%lld\n", ans, dp);
    return 0;
}
```

## 品酒大会

SA+并查集。

根据 $height$ 数组从大到小来统计，用并查集来合并。

```cpp
const int maxn = 3e5 + 100;
int n, m, a[maxn];
char ch[maxn];
namespace SA
{
int x[maxn * 2], y[maxn * 2], s[maxn], c[maxn], rk[maxn], h[maxn];
void main()
{
    for (int i = 1; i <= n; i++)
        c[x[i] = (ch[i] - 'a' + 1)]++, qmax(m, x[i]);
    for (int i = 1; i <= m; i++)
        c[i] += c[i - 1];
    for (int i = n; i >= 1; i--)
        s[c[x[i]]--] = i;
    for (int k = 1; k <= n; k <<= 1)
    {
        int p = 0;
        for (int i = n - k + 1; i <= n; i++)
            y[++p] = i;
        for (int i = 1; i <= n; i++)
            if (s[i] > k)
                y[++p] = s[i] - k;
        memset(c, 0, sizeof(c));
        for (int i = 1; i <= n; i++)
            c[x[y[i]]]++;
        for (int i = 1; i <= m; i++)
            c[i] += c[i - 1];
        for (int i = n; i >= 1; i--)
            s[c[x[y[i]]]--] = y[i];
        memcpy(y, x, sizeof(y));
        p = 2;
        x[s[1]] = 1;
        for (int i = 2; i <= n; i++)
            x[s[i]] = (y[s[i]] == y[s[i - 1]] && y[s[i] + k] == y[s[i - 1] + k]) ? p - 1 : p++;
        if (p > n)
            break;
        m = p;
    }
    for (int i = 1; i <= n; i++)
        rk[s[i]] = i;
    for (int i = 1, j, k = 0; i <= n; i++)
    {
        if (k)
            --k;
        j = s[rk[i] - 1];
        while (ch[i + k] == ch[j + k])
            ++k;
        h[i] = k;
    }
}
} // namespace SA
int fa[maxn], sz[maxn], Min[2][maxn], Max[2][maxn];
int gf(int x)
{
    if (fa[x] == x)
        return x;
    return fa[x] = gf(fa[x]);
}
void merge(int x, int y)
{
    x = gf(x);
    y = gf(y);
    if (x == y)
        return;
    fa[y] = x;
    sz[x] += sz[y];
    sz[y] = 0;

    int A[4];
    A[0] = Min[0][x];
    A[1] = Min[1][x];
    A[2] = Min[0][y];
    A[3] = Min[1][y];
    sort(A, A + 4);
    Min[0][x] = A[0];
    Min[1][x] = A[1];

    A[0] = Max[0][x];
    A[1] = Max[1][x];
    A[2] = Max[0][y];
    A[3] = Max[1][y];
    sort(A, A + 4);
    Max[0][x] = A[3];
    Max[1][x] = A[2];
}
vector<int> G[maxn];
ll ans[maxn][2];
using namespace SA;
int main()
{
#ifdef ylx
    freopen("p2178.in", "r", stdin);
    freopen("p2178.out", "w", stdout);
#endif
    n = read();
    scanf("%s", ch + 1);
    for (int i = 1; i <= n; i++)
    {
        a[i] = read();
        fa[i] = i;
        sz[i] = 1;
        Min[0][i] = Max[0][i] = a[i];
        Min[1][i] = 1e9 + 1;
        Max[1][i] = -1e9 + 1;
    }
    SA::main();
    for (int i = 1; i <= n; i++)
    {
        ans[i][1] = -2e18;
        if (rk[i] != 1)
            G[h[i]].push_back(i);
    }
    ll sum = 0;
    for (int i = n - 1, x; i >= 0; i--)
    {
        for (auto v : G[i])
        {
            x = gf(v);
            sum -= (ll)sz[x] * (sz[x] - 1) / 2;
            x = gf(s[rk[v] - 1]);
            sum -= (ll)sz[x] * (sz[x] - 1) / 2;
            merge(v, s[rk[v] - 1]);
            x = gf(v);
            sum += (ll)sz[x] * (sz[x] - 1) / 2;
            ans[i][1] = max(ans[i][1], (ll)Min[0][x] * Min[1][x]);
            ans[i][1] = max(ans[i][1], (ll)Max[0][x] * Max[1][x]);
        }
        ans[i][0] = sum;
        if (ans[i + 1][0] != 0)
            ans[i][1] = max(ans[i][1], ans[i + 1][1]);
        if (!sum)
            ans[i][1] = 0;
    }
    for (int i = 0; i < n; i++)
    {
        printf("%lld %lld\n", ans[i][0], ans[i][1]);
    }
    return 0;
}
```

## 小园丁与老司机

没写。