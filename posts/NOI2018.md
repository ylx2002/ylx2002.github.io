---

title: NOI2018 补题
slug: NOI2018
date: 2019-06-08 13:00:00 UTC+08:00
tags: 重构树, 图论, dp, 组合, 线段树, 线段树合并, SAM, 字符串, 数据结构
category: OI
author: yilanxuan
has_math: true

---

完成度：3/6

<!-- TEASER_END:  -->

[传送门](https://www.luogu.org/fe/problem/list?tag=77%7C35&page=1)

## 归程 

其实很模板，Kruskal 重构树

```cpp
const int maxn = 2e5 + 100;
const int maxm = 4e5 + 100;
int T, n, m, Q1;
struct node1
{
	int x, y, l, h;
} a[maxm];
bool cmp(node1 aa, node1 bb)
{
	return aa.h > bb.h;
}
int po[maxn], to[maxm * 2], ne[maxm * 2], w[maxm * 2], id;
int dis[maxn], INF;
void add(int x, int y, int z)
{
	id++;
	to[id] = y;
	ne[id] = po[x];
	w[id] = z;
	po[x] = id;
}
struct node
{
	int x, d;
	bool operator<(const node &A) const
	{
		return x > A.x;
	}
};
priority_queue<node> Q;
node A, B;
struct que
{
	int x, y, id, ans;
} q[maxn];
bool cmp1(que xx, que yy)
{
	return xx.y > yy.y;
}
int K, S, ID;
int fa[maxm];
int Po[maxn], To[maxm << 1], Ne[maxm << 1], cnt, Id;
int gf(int x)
{
	if (fa[x] == x)
		return x;
	fa[x] = gf(fa[x]);
	return fa[x];
}
void Add(int x, int y)
{
	Id++;
	To[Id] = y;
	Ne[Id] = Po[x];
	Po[x] = Id;
}
pair<int, int> p[maxm];
void work()
{
	for (int i = 1; i <= n * 2; i++)
		fa[i] = i;
	for (int i = 1; i <= n; i++)
		p[i].second = dis[i];
	sort(a + 1, a + m + 1, cmp);
	cnt = n;
	int Tot = 0;
	for (int i = 1; i <= m; i++)
	{
		int u = a[i].x, v = a[i].y;
		int fu = gf(u), fv = gf(v);
		if (fu != fv)
		{
			++cnt;
			Add(cnt, fu);
			Add(cnt, fv);
			fa[fu] = cnt;
			fa[fv] = cnt;
			p[cnt].first = a[i].h;
			Tot++;
		}
		if (Tot == n - 1)
			break;
	}

	return;
}
int main()
{
#ifdef ylx
	freopen("return.in", "r", stdin);
	freopen("return.out", "w", stdout);
#endif
	T = read();
	while (T--)
	{
		n = read();
		m = read();
		id = 0;
		memset(po, 0, sizeof(po));
		for (int i = 1; i <= m; i++)
		{
			a[i].x = read(), a[i].y = read(), a[i].l = read(), a[i].h = read();
			add(a[i].x, a[i].y, a[i].l);
			add(a[i].y, a[i].x, a[i].l);
		}
		memset(dis, 0x3f3f3f3f, sizeof(dis));
		INF = dis[0];
		dis[1] = 0;
		A.x = 0;
		A.d = 1;
		Q.push(A);
		while (!Q.empty())
		{
			A = Q.top();
			Q.pop();
			int u = A.d;
			if (A.x > dis[u])
				continue;
			for (int i = po[u]; i; i = ne[i])
			{
				if (w[i] + dis[u] < dis[to[i]] || (dis[to[i]] == INF))
				{
					dis[to[i]] = w[i] + dis[u];
					B.d = to[i];
					B.x = dis[to[i]];
					Q.push(B);
				}
			}
		}
		Q1 = read();
		K = read(), S = read();
		work();
	}
	return 0;
}
```

## 冒泡排序

$f\_{i,j}$ 表示填了 $i$ 个位置后，最大的数是 $j$ 到达最终状态的方案数。初始状态：$f\_{n,n}=1$

$$
f\_{i,j}=[i+1\le j]f\_{i+1,j}+\sum \_{k=j+1}^n f\_{i+1,k}\\\
=[i+1\le j]f\_{i+1,j}+f\_{i,j+1}
$$

时间复杂度 $O(n^2)$  ，期望得分 $80$

翻转一下下标 $i$ -> $n-i$ ，得到 $F\_{0,0}=1$

$$
F\_{i,j}=[i-1\ge j] F\_{i-1,j}+F\_{i,j-1}
$$

发现是`Catalan` 数   

$$
F\_{i,j}={i+j\choose j}-{i+j\choose j-1}
$$

然后我们枚举 $i$ 表示前 $i-1$ 位是卡着上限的。

假设前面都是合法的。

$p$ 表示这一位最小的合法状态，直接和前面的所有数取Max然后+1。

方案就是

$$
\sum\_{k=p}^n f\_{i,k}\\\
=\sum\_{k=0}^{n-p} F\_{n-i,k}\\\
=\sum\_{k=0}^{n-p} {n-i+k\choose k}-{n-i+k\choose k-1}\\\
=\sum\_{k=0}^{n-p} {n-i+k\choose k}-\sum\_{k=0}^{n-p}{n-i+k\choose k-1}
$$

这东西是可以拆的。

$$
{j+i+1\choose i}={j+i\choose i}+{j+i\choose i-1}={j+i\choose i}+{j+i-1\choose i-1}+{j+i-1\choose i-2}=\dots=\sum\_{k=0}^{i}{j+k\choose k}
$$

所以方案数就是

$$
{2n-i-p+1\choose n-p}-{2n-i-p+1\choose n-p-1}
$$

```cpp
const int maxn = 1e6 + 2e5 + 100;
const ll mod = 998244353;
int T, n, m, _m;
int a[maxn], vis[maxn];
int jie[maxn], ny[maxn];
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
void init()
{
    n = 1e6 + 2e5;
    jie[0] = 1;
    for (int i = 1; i <= n; i++)
        jie[i] = (ll)jie[i - 1] * i % mod;
    ny[n] = ksm(jie[n], mod - 2);
    for (int i = n; i >= 1; i--)
        ny[i - 1] = (ll)ny[i] * i % mod;
}
ll C(int x, int y)
{
    if (x < y)
        return 0;
    return (ll)jie[x] * ny[x - y] % mod * ny[y] % mod;
}
int main()
{
#ifdef ylx
    freopen("p4769.in", "r", stdin);
    freopen("p4769.out", "w", stdout);
#endif
    T = read();
    init();
    while (T--)
    {
        n = read();
        for (int i = 1; i <= n; i++)
            a[i] = read(), vis[i] = 0;
        m = 0, _m = 1; // m 前趋 max , _m 其中一个子序列到几了(一定是1开头)
        int p;
        ll ans = 0;
        for (int i = 1; i <= n; i++)
        {
            p = max(m, a[i]) + 1; // 最小可行值
            ans = ans + mod + C(n + n - i - p + 1, n - p) - C(n + n - i - p + 1, n - p - 1);
            ans %= mod;
            if (a[i] > m)
                m = a[i];
            else if (a[i] != _m)
                break;
            vis[a[i]] = 1;
            while (vis[_m])
                _m++;
        }
        printf("%lld\n", ans);
    }
    return 0;
}
//2019年 06月 17日 星期一 15:48:43 CST
```



## 你的名字

线段树合并维护right数组。

```cpp
namespace ST
{
const int maxn = 3e7 + 100;
int l[maxn], r[maxn], rt[maxn], cnt;
int query(int d, int x, int y, int L, int R)
{
    if (!d)
        return 0;
    if (L <= x && y <= R)
        return 1;
    int mid = (x + y) >> 1;
    int s = 0;
    if (L <= mid)
        s |= query(l[d], x, mid, L, R);
    if (R > mid)
        s |= query(r[d], mid + 1, y, L, R);
    return s;
}
void update(int &d, int x, int y, int p)
{
    if (!d)
        d = ++cnt;
    if (x == y)
        return;
    int mid = (x + y) >> 1;
    if (p <= mid)
        update(l[d], x, mid, p);
    else
        update(r[d], mid + 1, y, p);
}
int merge(int x, int y)
{
    if (!x || !y)
        return x + y;
    int Z = ++cnt;
    l[Z] = merge(l[x], l[y]);
    r[Z] = merge(r[x], r[y]);
    return Z;
}
} // namespace ST
const int maxn = 2e6 + 100;
const int maxm = 26;
int n, m;
char s[maxn], t[maxn];
int B[maxn], C[maxn];
struct SAM
{
    int fa[maxn], ch[maxn][maxm];
    int dist[maxn];
    int lim[maxn];
    int np, p, la, cnt, rt, q, nq;
    void insert(int x)
    {
        p = la;
        np = ++cnt;
        la = np;
        dist[np] = dist[p] + 1;
        for (; p && ch[p][x] == 0; p = fa[p])
            ch[p][x] = np;
        if (!p)
            fa[np] = rt;
        else
        {
            q = ch[p][x];
            if (dist[q] == dist[p] + 1)
                fa[np] = q;
            else
            {
                nq = ++cnt;
                dist[nq] = dist[p] + 1;
                fa[nq] = fa[q];
                fa[q] = fa[np] = nq;
                memcpy(ch[nq], ch[q], sizeof(ch[q]));
                for (; ch[p][x] == q; p = fa[p])
                    ch[p][x] = nq;
            }
        }
    }
    void init()
    {
        for (int i = 1; i <= cnt; i++)
        {
            dist[i] = 0;
            fa[i] = 0;
            lim[i] = 0;
            for (int j = 0; j < 26; j++)
                ch[i][j] = 0;
        }
        rt = la = cnt = 1;
    }
    void initS()
    {
        scanf("%s", s + 1);
        n = strlen(s + 1);
        for (int i = 1; i <= n; i++)
        {
            insert(s[i] - 'a');
            ST::update(ST::rt[la], 1, n, i);
        }
        for (int i = 1; i <= cnt; i++)
            B[dist[i]]++;
        for (int i = 1; i <= cnt; i++)
            B[i] += B[i - 1];
        for (int i = 1; i <= cnt; i++)
            C[B[dist[i]]--] = i;
        for (int i = cnt, x; i >= 2; i--)
        {
            x = C[i];
            ST::rt[fa[x]] = ST::merge(ST::rt[fa[x]], ST::rt[x]);
        }
    }
    void buildT()
    {
        ll sum = 0;
        for (int i = 2; i <= cnt; i++)
        {
            sum += max(0, dist[i] - max(dist[fa[i]], lim[i]));
        }
        printf("%lld\n", sum);
    }
} T1, T2;
void slove()
{
    scanf("%s", t + 1);
    m = strlen(t + 1);
    T2.init();
    int l = read(), r = read();
    int len = 0, now = T1.rt, c;
    for (int i = 1; i <= m; i++)
    {
        c = t[i] - 'a';
        T2.insert(c);
        while (true)
        {
            if (T1.ch[now][c] && ST::query(ST::rt[T1.ch[now][c]], 1, n, l + len, r))
            {
                now = T1.ch[now][c];
                ++len;
                break;
            }
            if (!len)
                break;
            --len;
            if (len == T1.dist[T1.fa[now]])
                now = T1.fa[now];
        }
        T2.lim[T2.la] = T2.lim[T2.cnt] = len;
    }
    T2.buildT();
}
int main()
{
#ifdef ylx
    freopen("p4770.in", "r", stdin);
    freopen("p4770.out", "w", stdout);
#endif
    T1.init();
    T1.initS();
    int T = read();
    while (T--)
    {
        slove();
    }
}
```