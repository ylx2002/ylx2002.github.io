---

title: GYM 101933 乱做
slug: gym101933
date: 2019-04-18 19:00:00 UTC+08:00
tags: dp, 搜索, 状压, 构造, 二分, 最短路, 图论
category: OI 
author: yilanxuan
has_math: true

---

一只鸽子随便点开的一套题。

<!-- TEASER_END:  -->

tag : dp

BC 是签到，FHI 没做/没看

G暂时不会。

## A
### 题意

有 $n$ 只青蛙在井下，井深为 $d$。

每只青蛙有三个参数 $l\_i,w\_i,h\_i$，

$l\_i$ 表示一只青蛙跳的高度，$w\_i$ 表示青蛙的体重，$h\_i$ 表示高。

青蛙可以踩在地上，也可以踩在其他青蛙上面，对于一只青蛙 $i$ ，踩在它上面的青蛙的 $\sum\_{j} w\_j < w\_i$ 。

当一只青蛙所站的高度 $+h\_i$ 大于 $l\_i$ 的时候可以跳出井。青蛙不可以踩在已出井的青蛙上。

求最多可以让多少只青蛙出井。

$n\le 10^5$，$d,h_i,l_i\le10^8$，$\sum w_i <10^8$

时间限制：3s，空间限制：512MB

### 做法

很容易发现，$w_i$ 小的青蛙不会对 $w_i$ 大的影响。

于是我们出井的顺序应该是 $w_i$ 小的青蛙先出井。

我们需要求出，让剩下的青蛙堆起来，且可以再加一只 $w_i$ 重的青蛙的最大的高度。

$dp_i$ 表示剩余可以加的重量为 $i$，时候的最大高度。

转移：$dp\_{\min(w\_i-1,j-w\_i)}=\max(dp\_{\min(w\_i-1,j-w\_i)},dp\_j+h\_i)$

时间复杂度：$O(n\sum w\_i)$

然而有效的转移 $j$ 的取值为 $w\_i->2w\_i$，其他的可以用后缀Max来转移。

时间复杂度 $O(\sum w\_i)$

### 代码

```cpp
const int maxm = 1e8 + 1;
const int maxn = 1e5 + 10;
int n, d;
struct node
{
	int l, w, h;
} a[maxn];
int dp[maxm],sum;
bool cmp(node aa, node bb)
{
	if (aa.w == bb.w)
		return aa.h > bb.h;
	return aa.w > bb.w;
}
int main()
{
#ifdef ylx
	freopen("A.in", "r", stdin);
	freopen("A.out", "w", stdout);
#endif
	n = read(), d = read();
	for (int i = 1; i <= n; i++)
	{
		a[i].l = read(), a[i].w = read(), a[i].h = read();
	}
	sort(a + 1, a + n + 1, cmp);
	int p = a[1].w;
	for (int i = 1; i <= n; i++)
	{
		while (p > a[i].w)
		{
			--p;
			dp[p] = max(dp[p], dp[p + 1]);
		}
		if (dp[a[i].w] + a[i].l > d) // 可以跳出去
			sum++;
		if (a[i].w * 2 > maxm - 1)
		{
			dp[a[i].w - 1] = a[i].h;
			continue;
		}
		for (int j = a[i].w * 2; j >= a[i].w; j--)
		{
			qmax(dp[min(j - a[i].w, a[i].w - 1)], dp[j] + a[i].h);
		}
	}
	printf("%d\n", sum);
	return 0;
}
```

## D
### 题意

~~阅读理解题。~~

给你一个图，$n$ 个点，$m$ 条边，带边权 $d_i$。

你有 $k$ 个物品，在 $t_i$ 时刻后在1号点取走一个物品，并且尽快送达到 $u_i$，有个规定送达时间 $s_i$。保证 $s_i$ 和 $t_i$ 是单调的，并且如果 $i< j$，则物品 $i$ 需要先送达。

求超时最久的物品最少超时了多久。

$2\le n\le 1000,1\le m \le 5000$，$0\le d_i \le 10^8$

$k\le 1000$，$0\le s\_i\le t\_i\le 10^8$

### 做法

一定是选择一段连续的区间去同时去`1`号点取，然后按照顺序放到对应的点。

因为如果取走了一个物品，不放到对应的位置，去`1`号取其他物品，相当于之前没有取这个物品。

二分答案。

$f_i$ 表示处理完 $i$ 号（没有回到1号点）物品，最早的时刻。

转移：从后往前枚举转移点 $j$，取物品的时间为$t_i$，相当于在到达 $u_j$ 之前要到达一下 $u_{j-1}$ 点，后面的点的最后到达时间可以计算出来。

很显然只需要记录当前超时最久的物品超过的时间就可以知道当前状态是否合法。

开始还要预处理一个多源最短路。

时间复杂度：$O(n^2\log m+n^2\log(2\times 10^11))$

### 代码
```cpp
const int maxn = 1010;
const int maxm = 5010;
int n, m;
ll f[maxn][maxn];
namespace Graph
{
int po[maxn], id;
int to[maxm << 1], ne[maxm << 1], w[maxm << 1];
ll dis[maxn];
struct node
{
    int x;
    ll w;
    bool operator<(const node &a) const
    {
        return w > a.w;
    }
};
int vis[maxn];
void add(int x, int y, int z)
{
    id++;
    to[id] = y, w[id] = z, ne[id] = po[x], po[x] = id;
}
priority_queue<node> q;
void dij(int S)
{
    memset(dis, 0x3f, sizeof(dis));
    dis[S] = 0;
    q.push((node){S, 0});
    node A;
    int u, v;
    while (!q.empty())
    {
        A = q.top(), q.pop();
        u = A.x;
        if (dis[u] != A.w)
            continue;
        for (int i = po[u]; i; i = ne[i])
        {
            v = to[i];
            if (dis[v] > dis[u] + w[i])
            {
                dis[v] = dis[u] + w[i];
                q.push((node){v, dis[v]});
            }
        }
    }
    for (int i = 1; i <= n; i++)
        f[S][i] = dis[i];
}
void find_dis()
{
    for (int i = 1; i <= n; i++)
        dij(i);
}
} // namespace Graph
int k;
int S[maxn], U[maxn], T[maxn];
ll dp[maxn], Inf;
int flag;
bool check(ll Ans)
{
    ll TLE, lst, s, now;
    dp[0] = 0;
    for (int i = 1; i <= k; i++)
    {
        dp[i] = max(dp[i - 1] + f[U[i - 1]][1], (ll)S[i]) + f[1][U[i]];
        TLE = dp[i] - T[i]; //TLE最久的超了多久。
        lst = dp[i];        //到达 U[j + 1] 点的时间。
        if (TLE > Ans)
            dp[i] = Inf;
        s = 0;                           //从 U[j+1] 出发到达后面所有点的总时间
        for (int j = i - 1; j >= 1; j--) //枚举开始位置
        {
            now = max(dp[j - 1] + f[U[j - 1]][1], (ll)S[i]) + f[1][U[j]];
            TLE = TLE + (now + f[U[j]][U[j + 1]]) - lst;
            TLE = max(TLE, now - T[j]);
            s += f[U[j]][U[j + 1]];
            if (TLE <= Ans)
                dp[i] = min(dp[i], now + s);
            lst = now;
        }
        if (dp[i] >= Inf)
            return false;
    }
    return true;
}
int main()
{
#ifdef ylx
    freopen("D.in", "r", stdin);
    freopen("D.out", "w", stdout);
#endif
    memset(dp, 0x3f, sizeof(dp));
    Inf = dp[0];
    U[0] = 1;
    n = read(), m = read();

    int F2 = 0;

    for (int i = 1, x, y, z; i <= m; i++)
    {
        x = read(), y = read(), z = read();
        if (i == 1)
            F2 = x;
        Graph::add(x, y, z);
        Graph::add(y, x, z);
    }
    Graph::find_dis();
    k = read();
    for (int i = 1; i <= k; i++)
    {
        T[i] = read(), U[i] = read(), S[i] = read();
    }
    ll L = 0, R = 99800199999001 + 10, ans, mid;
    while (L <= R)
    {
        mid = (L + R) >> 1;
        if (check(mid))
            R = mid - 1, ans = mid;
        else
            L = mid + 1;
    }
    printf("%lld\n", ans);
    return 0;
}
//2019年 06月 15日 星期六 09:20:26 CST
```

## E

### 题意

两个人玩卡牌游戏，玩家1有 $n(\le 5)$ 个随从，玩家2有 $m(\le 5)$ 个随从，每个随从有 $x\_i(x\_i\le 6)$ 点血。

现在随机分配 $d$ 点伤害，求玩家2 所有的随从血量都为0的概率。

### 做法

空间比时间惨系列。

直接压血量不行。

然而可以惊奇的发现(~~看sol可知~~) 当多个随从血量一样时可以合并，然后状态数就符合要求了。

~~我太蔡了，想不到啊。~~

wa了一次，原因：记忆化没有记忆化。

### 代码

```cpp
const int maxn = 110;
int n, m, d, as, bs;
int a[maxn], b[maxn];
unordered_map <ll,double> p;
int c[2][maxn];
ll build()
{
	ll sum = 0;
	for (int i = 1; i <= 6; i++)
		sum = sum * 10 + c[0][i];
	for (int i = 1; i <= 6; i++)
		sum = sum * 10 + c[1][i];
	return sum;
}
double dfs(ll now, int P)
{
	if (now < 1000000)
		return 1;
	if (P == 0)
		return 0;
	if (p.count(now))
		return p[now];
	int sum=0;
	double nxt,ss=0;
	for (int j = 0; j < 2; j++)
		for (int i = 1; i <= 6; i++)
			sum += c[j][i];
	for (int j = 0; j < 2; j++)
		for (int i=1;i<=6;i++)
		if (c[j][i])
		{
			c[j][i]--;
			c[j][i - 1]++;
			nxt = dfs(build(), P - 1);
			c[j][i]++;
			c[j][i - 1]--;
			ss += (double)c[j][i] / (double)sum * nxt;
		}
	p[now]=ss;
	return ss;
}
int main()
{
	n = read(), m = read(), d = read();
	for (int i = 1; i <= n; i++)
	{
		a[i] = read(), as += a[i];
		c[1][a[i]]++;
	}
	for (int i = 1; i <= m; i++)
	{
		b[i] = read(), bs += b[i];
		c[0][b[i]]++;
	}
	if (as + bs <= d)
	{
		putchar('1');
		return 0;
	}
	if (bs > d)
	{
		putchar('0');
		return 0;
	}
	ll x = build();
	double ans = dfs(x, d);
	printf("%.8f",ans);
}
```

## G

### 题意

有 $m$ 支球队，每支球队有 $n$ 个人，要求构造不超过 $(m-1)\times n+1$ 轮比赛，使每个人都与非本支球队的人进行了比赛（每个人需要进行 $(m-1)\times n$ 次比赛。

$1\le n \le 25,2\le m \le 25,nm\le 100$

### 做法

不会。

### 代码

没写。

## J

### 题意

给定四个数字 $a,b,c,d\le 10^9$，要求构造一个字符串，使

- `00` 作为子序列出现 $a$ 次。
- `01` 作为子序列出现 $b$ 次。
- `10` 作为子序列出现 $c$ 次。
- `11` 作为子序列出现 $d$ 次。

### 做法

~~一道很眼熟的题，然而秒不掉。~~

如果有 $x$ 个 `0`，则有 $\frac{x(x-1)}{2}$ 个 `00`，`1` 同理。顺利判掉部分的无解，并求出`0`和`1`的个数。

设有 $n$ 个 `0`，$m$ 个 `1` ，

若序列是`00..00011..111`，则有 `01` $n\times m$ 对。 可以知道，$b+c=n \times m$。

我们先放入所有的 `0`， 此时序列长这样 `000..000` ，在位置 $i$ 插入一个 `1`,`01` 的对数 $+i$，`10` 的对数 $+(n-i)$ 。 

我们可以在位置 $b\mod n$ 插入一个`1`，然后剩余的 `1` 只需要插入到开头和结尾即满足条件。

还是比较签到了。

**a=0,b=0要特判**。 这里wa了几次qwq。

### 代码

```cpp
int a, b, c, d, n, m;
void Exit()
{
	printf("impossible\n");
	exit(0);
}
int find(int x)
{
	for (ll i = 1; i <= 100000; i++)
	{
		if (i * (i - 1) / 2 == (ll)x)
			return i;
		if (i * (i - 1) / 2 > x)
			return -1;
	}
	return -1;
}
int main()
{
	a = read(), b = read(), c = read(), d = read();
	n = find(a);
	m = find(d);
	if (n == -1 || m == -1)
		Exit();
	if ((n == 1) && (m == 1))
	{
		if (b == 0 && c == 0)
		{
			putchar('0');
			return 0;
		}
		if (b == 1 && c == 0)
		{
			putchar('0'), putchar('1');
			return 0;
		}
		if (c == 1 && b == 0)
		{
			putchar('1'), putchar('0');
			return 0;
		}
		Exit();
	}
	if (n == 1)
		if (b == 0 && c == 0)
		{
			n = 0;
			while (m--)
				putchar('1');
			return 0;
		}
	if (m == 1)
		if (b == 0 && c == 0)
		{
			m = 0;
			while (n--)
				putchar('0');
			return 0;
		}
	if (b + c != (ll)(n * m))
		Exit();
	int x = b % n;
	c /= n;
	b /= n;
	for (int i = 1; i <= c; i++)
		putchar('1');
	for (int i = 1; i <= n; i++)
	{
		putchar('0');
		if (i == x)
			putchar('1');
	}
	for (int i = 1; i <= b; i++)
		putchar('1');
	return 0;
}
```

## K

### 题意

给你 $n$ 个点的树，$k$ 种颜色。

要求给这棵书染色，使相邻的节点颜色互不相同。

求对这棵树染色的方案数。($\mod = 10^9+7$)

### 做法

$f(i)$ 表示**恰好**用 $i$ 种颜色染一棵树的方案数。

$g(i)$ 表示**最多**用 $i$ 种颜色染一棵树的方案数。

$$
g(i)=\sum\_{j=0}^i {i \choose j} f(i)
$$

$$
g(i)=i\times (i-1)^{n-1}
$$

$$
f(i)  = \sum\_{j=0}^i (-1)^{i-j}{i \choose j}g(j)
$$

$$
=\sum \_{j=0}^i (-1) ^{i-j}{i\choose j} j\times (j-1) ^{n-1} 
$$

~~树题不要读树。~~

### 代码

```cpp
const int maxn = 2510;
const ll mod = 1e9 + 7;
int n, k;
ll f[maxn], g[maxn], ny[maxn], jie[maxn];
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
ll C(int x, int y)
{
    if (x < y)
        return 0;
    return jie[x] * ny[y] % mod * ny[x - y] % mod;
}
int main()
{
    n = read(), k = read();
    for (int i = 1; i <= n; i++)
        g[i] = i * ksm(i - 1, n - 1) % mod;
    jie[0] = ny[0] = 1;
    for (int i = 1; i <= n; i++)
    {
        jie[i] = jie[i - 1] * i % mod;
        ny[i] = ksm(jie[i], mod - 2);
    }
    for (int i = k; i <= k; i++)
        for (int j = 0; j <= i; j++)
            if ((i - j) & 1)
            {
                f[i] -= C(i, j) * g[j] % mod;
                f[i] %= mod;
            }
            else
            {
                f[i] += C(i, j) * g[j] % mod;
                f[i] %= mod;
            }
    printf("%lld\n", f[k]);
    return 0;
}
```

