---

title: 2021 十一月乱做
slug: 202111training
date: 2021-11-01 12:00:00 UTC+08:00
tags: 图论, 2-SAT, 缩点, bitset, 分块, 前缀和, 线段树, 数据结构, floyd, hash, 高斯消元, 边双, 组合数学, 计数, dp, 容斥
category:  OI
author: yilanxuan
has_math: true

---

记录十一月份写的一些题。

希望自己不要继续当一只鸽子。

<!-- TEASER_END:  -->

## 11 月开的训练

[2018-2019 9th BSUIR Open Programming Championship. Semifinal](https://codeforces.com/gym/102439)

[2018-2019 9th BSUIR Open Programming Championship. Final](https://codeforces.com/gym/102441)

[The 2021 ICPC Asia Jinan Regional Contest](https://pintia.cn/market/item/1459833348620926976)

[The 2021 CCPC Guilin Onsite](https://codeforces.com/gym/103409)

[The 2021 ICPC Asia Shenyang Regional Contest](https://codeforces.com/gym/103427)

[The 2021 CCPC Weihai Onsite](https://codeforces.com/gym/103428) 有趣的题会写在 12 月记录

[2021-2022 ACM-ICPC Brazil Subregional Programming Contest](https://codeforces.com/gym/103388) 同上

然后 CCPC2021 哈尔滨的相关内容也在同上。

## GYM102439B Varvara and matrix

### 题意

给定一个 $n\times m$ 的矩阵，每个位置有一个数字 $a_{i,j}$。要求给 $a_{i,j}=0$ 的位置填上 $A$ 或者 $B$（ $A,B$ 是给定的），使得四个顶点权值相同的四边形的数目不增加。

保证每一行，每一列最多只有一个 0，$n,m\le 10^3$。

### 做法

第一步，不考虑 0 与 0 之间的关系的情况下，判断每个位置是否可以填 A,B，这个过程使用 `<bitset>` 进行维护。

第二步，两两枚举 0 点，按照 2-sat 的方式建边。（两个点不能同时取 A，两个点不能同时取 B）。

第三步，反向建图，用拓扑排序构造可行解。如果要字典序最小则需要使用优先队列。

### 代码

```cpp
const int maxn = 1e3 + 10;
int vis[maxn][2]; // vis[x][0/1] 表示点 x 是否可以为 0 or 1
int n, m, A, B;
int a[maxn][maxn], b[maxn][maxn], id; // id 表示当前点的编号
struct node
{
    int x, y, p;
} c[maxn * 2];
int ans[maxn][maxn];
bitset<1005> p[maxn];
void work(int tp)
{
    for (int i = 1; i <= n; i++)
        p[i].reset();
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            if (a[i][j] == tp)
                p[i].set(j);
    for (int i = 1, x, y; i <= id; i++)
    {
        x = c[i].x;
        y = c[i].y;
        for (int j = 1; j <= n; j++)
            if (j != x)
            {
                if (p[j][y] && ((p[x] & p[j]).count()))
                {
                    vis[i][tp] = 0;
                    break;
                } else vis[i][tp] = 1;
            }
    }
}
vector<int> G[maxn * 2];
int dfn[maxn * 2], low[maxn * 2], sccid, top, flag, scc[maxn * 2];
stack<int> S;
void tarjan(int x) //缩点
{
    S.push(x);
    dfn[x] = ++top;
    low[x] = dfn[x];
    int y = 0;
    for (auto y : G[x])
    {
        if (dfn[y] == 0)
        {
            tarjan(y);
            qmin(low[x], low[y]);
        }
        else if (scc[y] == 0)
            qmin(low[x], dfn[y]);
    }
    if (low[x] == dfn[x])
    {
        ++sccid;
        while (S.top() != x)
        {
            scc[S.top()] = sccid;
            S.pop();
        }
        scc[S.top()] = sccid;
        S.pop();
    }
}
void add(node A, node B)
{
    //00
    if (a[A.x][B.y] == 0 && a[B.x][A.y] == 0 && vis[A.p][0] == 1 && vis[B.p][0] == 1) // 两个0不能同时选
    {
        G[A.p * 2].push_back(B.p * 2 - 1);
        G[B.p * 2].push_back(A.p * 2 - 1);
    }
    //11
    if (a[A.x][B.y] == 1 && a[B.x][A.y] == 1 && vis[A.p][1] == 1 && vis[B.p][1] == 1) // 两个1不能同时选
    {
        G[A.p * 2 - 1].push_back(B.p * 2);
        G[B.p * 2 - 1].push_back(A.p * 2);
    }
}
vector<int> G1[maxn * 2];
int f[maxn * 2], idf; // 拓扑序
int rd[maxn * 2];
vector<int> gg[maxn * 2];
int Map[maxn * 2], sz[maxn * 2];
void buildgraph()
{
    for (int i = 1; i <= id + id; i++)
    {
        sz[scc[i]]++;
        gg[scc[i]].push_back(i);
    }
    for (int i = 1; i <= id + id; i++)
        if (sz[scc[i]] == 1)
            Map[scc[i]] = i;
    for (int i = 1, y; i <= id + id; i++) // 建反图
        for (auto y : G[i])
        {
            if (scc[i] != scc[y])
                G1[scc[y]].push_back(scc[i]), rd[scc[i]]++;
        }
}
void tp()
{
    queue<int> q;
    for (int i = 1; i <= sccid; i++)
        if (rd[i] == 0) q.push(i);
    while (!q.empty())
    {
        int x = q.front(), y, xx;
        q.pop();
        xx = Map[x];
        if (xx)
        {
            if (vis[(xx + 1) / 2][xx % 2] == 0 || ans[c[(xx + 1) / 2].x][c[(xx + 1) / 2].y]) // 无法取这个点
                continue;
        }
        // 取这个点
        int flag1 = 1;
        for (auto xx : gg[x])
            if (ans[c[(xx + 1) / 2].x][c[(xx + 1) / 2].y])
            {
                flag1 = 0;
                break;
            }
        if (!flag1)
            continue;
        for (auto xx : gg[x])
            ans[c[(xx + 1) / 2].x][c[(xx + 1) / 2].y] = xx % 2 ? B : A;
        f[x] = ++idf;
        for (auto y : G1[x])
        {
            rd[y]--;
            if (!rd[y]) q.push(y);
        }
    }
}
int main()
{
#ifdef ylx
    freopen("B.in", "r", stdin);
    freopen("B.out", "w", stdout);
#endif
    n = read(), m = read();
    A = read(), A = read(), B = read();
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
        {
            a[i][j] = read();
            if (a[i][j] == A)
                a[i][j] = 0, ans[i][j] = A;
            else if (a[i][j] == B)
                a[i][j] = 1, ans[i][j] = B;
            else if (a[i][j] == 0)
            {
                a[i][j] = -1; // 表示没有填数
                b[i][j] = ++id;
                c[id].x = i, c[id].y = j, c[id].p = id;
            }
            else
                ans[i][j] = a[i][j], a[i][j] = -2;
        }
    work(0);
    work(1);
 
    for (int i = 1; i <= id; i++)
        for (int j = i + 1; j <= id; j++) // 枚举两个关联点。
            add(c[i], c[j]);
    flag = 1;
    for (int i = 1; i <= id + id; i++)
        if (!dfn[i])
            tarjan(i);
    for (int i = 1; i <= id + id; i += 2)
        if (scc[i] == scc[i + 1] && vis[(i + 1) / 2][0] && vis[(i + 1) / 2][1])
        {
            printf("No\n");
            return 0;
        }
    buildgraph();
    tp();
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            if (!ans[i][j])
                flag = 0;
    if (!flag)
    {
        printf("No\n");
        return 0;
    }
    printf("Yes\n");
    for (int i = 1; i <= n; i++)
    {
        for (int j = 1; j <= m; j++)
            printf("%d ", ans[i][j]);
        putchar('\n');
    }
    return 0;
}
```

## GYM102441J Paternity Testing

### 题意

有一棵 $n$ 个点的树，$cnt(v,l,r)$ 表示点 $v$ 的子树内编号在区间 $[l,r]$ 的点的个数。$q$ 组询问，求 $\sum_{i=l}^r cnt(i,l,r)$。

### 做法

首先，问题转化后相当于求区间内有多少对点存在祖孙关系。

对于一个点对 $(u,v)$ 会产生 1 贡献的条件是：$l\le u,v\le r$ 并且存在父子关系（dfs 序在一定范围）。

1. 预处理【每个块】的【每个前缀】到【每个整块】的贡献，后缀同理。
具体操作是：前缀是对于每个块搞一个数组，表示dfs序为x的点在这个区间内产生的贡献是多少。后缀则使用树上差分，考虑当前后缀对后面每个块的答案，暴力更新。

2. 预处理出每个区间内部的答案。具体操作：暴力看两个点是否有贡献，然后使用区间 dp 进行合并贡献。

3. 预处理出【每个块】到【每个块】的答案。
具体操作：搞出块内答案，然后枚举自己左边的块。

4. 每个块内前缀和后缀都整一个数组存其按照 dfs 序排的点的编号，用于归并。
具体查询过程：dfs 序比自己的 dfs 序要大、比自己的 out 要小的计入答案。相当于总数目减去 dfs 序比自己小的，减去 dfs 序比自己 out 大的。

### 代码

```cpp
const int maxn = 5e4 + 10;
const int SZ = 257;
int n, q, u, v, N, l[SZ + 2], r[SZ + 2];
int fa[maxn], chafen[maxn];
vector<int> g[maxn];
int sz[maxn], dfn[maxn], dfnid, bk[maxn];                 //bk[i] 表示点 i 属于哪个块
int a[SZ + 2][SZ + 2][SZ + 2], b[SZ + 2][SZ + 2][SZ + 2]; // 前缀&后缀
// a[x,y,z] 表示当前是块x，前缀z和【块y到块x-1】产生的答案
int c[SZ + 2][SZ + 2][SZ + 2]; // 块内答案。
vector<int> pre[SZ + 2][SZ + 2], suf[SZ + 2][SZ + 2];
vector<int> pre1[SZ + 2][SZ + 2], suf1[SZ + 2][SZ + 2];
int ans1[SZ + 2][SZ + 2]; // ans1 存块外答案
int sum[SZ + 2][maxn];
void dfs(int x)
{
    dfn[x] = ++dfnid, sz[x] = 1;
    for (auto y : g[x])
    {
        fa[y] = x;
        dfs(y);
        sz[x] += sz[y];
    }
}
int query(int x, int y, int z) //查询 x..y 块里面，点 z 会产生的答案数。
{
    return sum[y][dfn[z]] - sum[x - 1][dfn[z]];
}
bool check(int x, int y) // check是否存在父子关系
{
    if (dfn[x] <= dfn[y] && dfn[y] < dfn[x] + sz[x]) return 1;
    if (dfn[y] <= dfn[x] && dfn[x] < dfn[y] + sz[y]) return 1;
    return 0;
}
void init1()
{
    int szz = sqrt(n);
    for (int i = 1; i <= n; i++)
    {
        bk[i] = (i - 1) / szz + 1;
        if (!l[bk[i]]) l[bk[i]] = i;
        r[bk[i]] = i;
    }
    N = bk[n];
    for (int i = 1; i <= N; i++)
    {
        // 维护sum数组
        for (int j = l[i]; j <= r[i]; j++)
        {
            sum[i][dfn[j]]++;
            sum[i][dfn[j] + sz[j]]--;
        }
        for (int j = 1; j <= n; j++) sum[i][j] += sum[i][j - 1];
        // 求区间内答案
        for (int j = l[i]; j <= r[i]; j++)
            for (int k = j; k <= r[i]; k++)
                if (check(j, k)) c[i][j - l[i] + 1][k - l[i] + 1]++;
        for (int len = 1; len <= r[i] - l[i]; len++)
            for (int j = 1; j + len <= r[i] - l[i] + 1; j++)
                c[i][j][j + len] += c[i][j][j + len - 1] + c[i][j + 1][j + len] - c[i][j + 1][j + len - 1];
        for (int j = 1; j <= n; j++)
            sum[i][j] += sum[i - 1][j];
    }
    for (int i = 1; i <= N; i++)
    {
        // 求前缀的答案
        for (int k = i - 1; k >= 1; k--)
            for (int j = l[i]; j <= r[i]; j++) // a[i][k][j]
                a[i][k][j - l[i] + 1] = a[i][k][j - l[i]] + query(k, i - 1, j);
        // 求后缀的答案
        for (int i = 1; i <= n; i++)
            chafen[i] = 0;
        for (int j = l[i]; j <= r[i]; j++)
            chafen[j] = 1;
        for (int j = n; j >= 1; j--) // 树上差分
            chafen[fa[j]] += chafen[j];
        for (int k = i - 1; k >= 1; k--)
            for (int j = r[k]; j >= l[k]; j--)
                b[k][i][j - l[k] + 1] += chafen[j];
    }
    for (int i = 1; i <= N; i++) // 块内求一次后缀和
        for (int k = 1; k < i; k++)
            for (int j = r[k] - 1; j >= l[k]; j--)
                b[k][i][j - l[k] + 1] += b[k][i][j - l[k] + 2];
    for (int i = 1; i <= N; i++) // 块之间答案求前缀和
        for (int k = i + 2; k <= N; k++)
            for (int j = r[i]; j >= l[i]; j--)
                b[i][k][j - l[i] + 1] += b[i][k - 1][j - l[i] + 1];
}
void init3()
{
    for (int i = 1; i <= N; i++)
    {
        ans1[i][i] = c[i][1][r[i] - l[i] + 1];
        for (int j = 1; j < i; j++)
            ans1[j][i] = ans1[j][i - 1] + ans1[i][i] + a[i][j][r[i] - l[i] + 1];
    }
}
pair<int, int> A[SZ + 2];
void init4()
{
    for (int i = 1, len; i <= N; i++)
    {
        len = r[i] - l[i] + 1;
        for (int j = l[i]; j <= r[i]; j++)
            A[j - l[i] + 1].first = dfn[j], A[j - l[i] + 1].second = j;
        sort(A + 1, A + len + 1);
        for (int j = 1; j <= len; j++)
        {
            //pre
            for (int k = A[j].second; k <= r[i]; k++)
                pre[i][k - l[i] + 1].push_back(A[j].second);
            //suf
            for (int k = l[i]; k <= A[j].second; k++)
                suf[i][k - l[i] + 1].push_back(A[j].second);
        }
        for (int j = l[i]; j <= r[i]; j++)
            A[j - l[i] + 1].first = dfn[j] + sz[j] - 1, A[j - l[i] + 1].second = j;
        sort(A + 1, A + len + 1);
        for (int j = 1; j <= len; j++)
        {
            //pre
            for (int k = A[j].second; k <= r[i]; k++)
                pre1[i][k - l[i] + 1].push_back(A[j].second);
            //suf
            for (int k = l[i]; k <= A[j].second; k++)
                suf1[i][k - l[i] + 1].push_back(A[j].second);
        }
    }
}
int qrest(vector<int> p, vector<int> q, vector<int> p1)
{
    vector<int> ve;
    ve.clear();
    int sum = p.size() * q.size(); // q 中点不可能是 p 中点的祖先
    vector<int>::iterator it2;
    int ss = 0;
    it2 = q.begin();
    for (auto x : p)
    {
        while (it2 != q.end() && dfn[*it2] <= dfn[x])
        {
            sum -= (p.size() - ss);
            ++it2;
        }
        ss++;
        if (it2 == q.end()) break;
    }
    ss = 0;
    it2 = q.begin();
    for (auto x : p1)
    {
        while (it2 != q.end() && dfn[*it2] <= dfn[x] + sz[x] - 1)
        {
            sum -= ss; ++it2;
        }
        ss++;
    }
    while (it2 != q.end())
    {
        sum -= ss; ++it2;
    }
    return sum;
}
int main()
{
#ifdef ylx
    freopen("J.in", "r", stdin);
    freopen("J.out", "w", stdout);
#endif
    n = read();
    for (int i = 2, x; i <= n; i++)
    {
        x = read();
        g[x].push_back(i);
    }
    dfs(1);
    init1();
    init3();
    init4();
    q = read();
    int ans = 0, L, R;
    while (q--)
    {
        u = read(), v = read();
        u = 1 + (u ^ ans) % n;
        v = 1 + (v ^ ans) % n;
        if (u > v) swap(u, v);
        ans = 0;
        if (bk[u] == bk[v])
        {
            L = bk[u];
            ans = c[L][u - l[L] + 1][v - l[L] + 1];
            printf("%d\n", ans);
            continue;
        }
        L = bk[u] + 1, R = bk[v] - 1;
        if (L <= R) // 完整块答案
        {
            ans = ans1[L][R];
            // 前面剩余与整块
            ans += b[bk[u]][R][u - l[bk[u]] + 1];
            // 后面剩余与整块
            ans += a[bk[v]][L][v - l[bk[v]] + 1];
        }
        //前面剩余
        L = bk[u], R = bk[v];
        ans += c[L][u - l[L] + 1][r[L] - l[L] + 1];
        //后面剩余
        ans += c[R][1][v - l[R] + 1];
        ans += qrest(suf[L][u - l[L] + 1], pre[R][v - l[R] + 1], suf1[L][u - l[L] + 1]);
        printf("%d", ans);
        putchar('\n');
    }
    return 0;
}
```

## ICPC The 2021 ICPC Asia Jinan Regional Contest

### E

#### 题意

给你一个大小为 $n$ 的环，环上有 $m$ 条边直线连接两个点。如果边 $(i,j),(p,q)$ 存在一个交点，则会产生 $w=(i+j)(p+q)$ 的贡献。

你需要选择两个点，删掉和这两个点相关的边，最大化总贡献。

$n\le 10^3,m\le 10^5$。

#### 做法

经典转化：对于边 $(x,y)$，钦定 $x\le y$。只有边 $([1,x-1],[x+1,y-1]),([x+1,y-1],[y+1,n])$ 会产生贡献。这个可以用二维前缀和进行维护。

求出所有的答案之后，再枚举删除点，暴力去掉相关边的贡献（考虑到每条边只会被算到两次，所以复杂度正确）。

假设枚举的点是 $x1,x2$，这两个点包含的边之间产生的贡献需要再用一个前缀和进行计算，然后减去。

#### 代码

```cpp
const int maxn = 1e3 + 10;
const int maxm = 1e5 + 10;
int n, m,eid;
vector<int> g[maxn];       // 每个点相关的边
pair<int, int> edge[maxm]; //所有边
ll a[maxn][maxn];          //前缀和
ll c[maxn][maxn];
void add(int x, int y)
{
    if (x > y) swap(x, y);
    edge[++eid].first = x, edge[eid].second = y;
    g[x].push_back(y);
    g[y].push_back(x);
    a[x][y] += x + y;
}
ll query(int xl, int xr, int yl, int yr)
{
    ll s = a[xr][yr] + a[xl - 1][yl - 1] - a[xl - 1][yr] - a[xr][yl - 1];
    return s;
}
ll query1(int xl, int xr, int y)
{
    return c[y][xr] - c[y][xl - 1];
}
ll sum, ans;
ll anss[maxn][maxn];
int vis[maxn][maxn];
ll get(int x, int y)
{
    if (y < x) swap(x, y);
    if (vis[x][y]) return anss[x][y];
    vis[x][y]=1;
    return anss[x][y] = (ll)(x + y) * (query(1, x - 1, x + 1, y - 1) + query(x + 1, y - 1, y + 1, n));
}
ll get1(int x, int y, int z)
{
    if (y < x)
        swap(x, y);
    ll s = anss[x][y];
    if (z < x || z > y) // 删掉与z的边相关的边。
        s -= (ll)(x + y) * query1(x + 1, y - 1, z);
    else s -= (ll)(x + y) * (query1(1, x - 1, z) + query1(y + 1, n, z));
    return s;
}
void init()
{
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= n; j++)
            a[i][j] += a[i][j - 1] + a[i - 1][j] - a[i - 1][j - 1];
    for (int i = 1, x, y; i <= m; i++)
    {
        x = edge[i].first, y = edge[i].second;
        sum = sum + get(x, y);
    }
    sum /= 2;
}
void change(int x, int y, int z)
{
    c[x][y] += z;
}
int main()
{
#ifdef ylx
    freopen("E.in", "r", stdin);
    freopen("E.out", "w", stdout);
#endif
    n = read(), m = read();
    for (int i = 1, x, y; i <= m; i++)
    {
        x = read(), y = read();
        add(x, y);
    }
    init();
    for (int i = 1; i <= n; i++)
    {
        ll s = 0, ss;
        for (auto x : g[i])
        {
            s += get(i, x);
            change(i, x, i + x);
        }
        for (int j = 1; j <= n; j++) c[i][j] += c[i][j - 1];
        for (int j = i + 1; j <= n; j++)
        {
            ss = 0;
            for (auto x : g[j])
                if (x != i) ss += get1(j, x, i);
            ans = max(ans, sum - ss - s);
        }
    }
    printf("%lld\n", ans);
    return 0;
}
```

### M

#### 题意

给出 $n(n\le 200)$ 矩形的坐标，对这些矩形进行染色，染色只染某条对角线的一次。四种染色方式分别用 0,1,2,3 表示，输出字典序最大的方案。

#### 做法

首先通过**半平面交**求出矩形两两之间什么样的染色方法会冲突。

然后考虑 2-SAT 如何求字典序最大的解。

建反图，然后从第一个矩形，字典序最大的情况开始取，再用一次拓扑排序判断剩余部分是否合法。（因为这里每个点有 4 种取法，所以不能够使用传统的 2-SAT 求字典序最小解。

```cpp
bool DFS(int s) // 2-SAT 求字典序最小模板
{
   if(col[s] == B) return 0;
   if(col[s] == R) return 1;
   col[s] = R ,col[s^1] = B;
   q.push(s);
   for(int k = list[s] ;k ;k = E[k].next)
   if(!DFS(E[k].to)) return 0;
   return 1;
}
bool solve(int n)
{
   memset(col ,W ,sizeof(col));
   for(int i = 0 ;i < n ;i ++)
   {
      if(col[i]) continue;
      while(!q.empty()) q.pop();
      if(!DFS(i))
      {
         while(!q.empty())
         {
            col[q.front()] = col[q.front()^1] = W;
            q.pop();
         }
         if(!DFS(i^1)) return 0;
      }
   }
   return 1;
}
```

### J

#### 题意

给出一个行列式，以及行列式的绝对值，判断正负。

#### 做法

真正的去计算 long long 也不够用。使用 hash，求出行列式取模的值，然后判断【给定的绝对值】在【模数相同的情况】是否与计算出来的值一致，如果一致则为正，否则为负。

这里为了方式被卡，使用了双 hash。

#### 代码

```cpp
#include <bits/stdc++.h>
using namespace std;
int T, n;
const int maxn = 110;
long long a[maxn][maxn], s1, s2, b[maxn][maxn];
const long long mod1 = 1e9 + 7;
const long long mod2 = 998244353;
string s;
void Gauss()
{
    long long res1 = 1;
    long long res2 = 1;
    int ans = 0;
    for (int i = 1; i <= n; i++)
    {
        for (int j = i + 1; j <= n; j++)
            while (a[j][i])
            {
                long long t = a[i][i] / a[j][i];
                for (int k = i; k <= n; k++)
                {
                    a[i][k] = (a[i][k] - t * a[j][k]) % mod1;
                }
                swap(a[i], a[j]);
                res1 = -res1;
            }
        res1 = (res1 * a[i][i]) % mod1;

        for (int j = i + 1; j <= n; j++)
            while (b[j][i])
            {
                long long t = b[i][i] / b[j][i];
                for (int k = i; k <= n; k++)
                {
                    b[i][k] = (b[i][k] - t * b[j][k]) % mod2;
                }
                swap(b[i], b[j]);
                res2 = -res2;
            }
        res2 = (res2 * b[i][i]) % mod2;
    }
    if (res1 < 0) res1 += mod1;
    if (res2 < 0) res2 += mod2;
    if (res1 == s1 && res2 == s2) putchar('+');
    else putchar('-');
    putchar('\n');
}
int main()
{
    scanf("%d", &T);
    while (T--)
    {
        scanf("%d", &n);
        cin >> s;
        int len = s.length();
        s1 = 0, s2 = 0;
        for (int i = 0; i < len; i++)
        {
            s1 = s1 * 10 + s[i] - '0';
            s2 = s2 * 10 + s[i] - '0';
            s1 %= mod1;
            s2 %= mod2;
        }
        for (int i = 1; i <= n; i++)
            for (int j = 1; j <= n; j++)
            {
                scanf("%lld", &a[i][j]);
                b[i][j] = a[i][j];
            }
        Gauss();
    }
    return 0;
}
```

## The 2021 ICPC Asia Shenyang Regional Contest

### H. Line Graph Matching

#### 题意

[传送门]([Problem - H - Codeforces](https://codeforces.com/gym/103427/problem/H))

转化后题意：一次可以覆盖两条相邻的边，一条边不能被重复覆盖，求最后被覆盖的边的边权和的最大值。$n,m\le2\times 10^5$，且保证图联通。

#### 做法

做法很简单，最后最多只要删除一条边。考虑每条边是否可以删除。

桥边：删除后两个连通块的边数都是偶数则可删。

非桥边：可删。

#### 代码

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
inline int read()
{
    char s;
    ll k = 0;
    while ((s = getchar()) != EOF && !(isdigit(s)))
        ;
    if (s == EOF)
        exit(0);
    while (isdigit(s))
        k = k * 10 + (s ^ '0'), s = getchar();
    return k;
}
int n, m;
namespace SSC
{
    const int maxn = 1e5 + 100;
    const int maxm = 2e5 + 100;
    int qwq;
    int sze[maxn];
    ll sum = 0, ans;
    struct tree
    {
        int po[maxn], to[maxn << 1], ne[maxn << 1], id;
        int w[maxn << 1];
        int sz[maxn], fa[maxn];
        void add(int x, int y, int z)
        {
            id++;
            to[id] = y, ne[id] = po[x], w[id] = z, po[x] = id;
        }
        void dfs(int x)
        {
            sz[x] = sze[x];
            for (int i = po[x]; i; i = ne[i])
                if (to[i] != fa[x])
                {
                    sz[x]++;
                    fa[to[i]] = x;
                    dfs(to[i]);
                    if (sz[to[i]] % 2 == 0)
                        ans = min(ans, (ll)w[i]);

                    sz[x] += sz[to[i]];
                }
        }
        void init()
        {
            memset(po, 0, sizeof(po));
            id = 0;
        }
        void work()
        {
            for (int i = 1; i <= qwq; i++)
                sz[i] = 0, fa[i] = 0;
            dfs(1);
        }
    } t;

    int block[maxn];
    int po[maxn], to[maxm << 1], ne[maxm << 1], w[maxm << 1], id;
    int bridge[maxm << 1], dfs_time;
    int dfn[maxn], low[maxn];

    void add(int x, int y, int z)
    {
        id++;
        to[id] = y, w[id] = z, ne[id] = po[x], po[x] = id;
    }
    void dfs(int x, int pre)
    {
        ++dfs_time;
        dfn[x] = low[x] = dfs_time;
        for (int i = po[x]; i; i = ne[i])
        {
            if (!dfn[to[i]])
            {
                dfs(to[i], i);
                qmin(low[x], low[to[i]]);
                if (low[to[i]] > dfn[x]) //qwq
                    bridge[i] = bridge[i ^ 1] = 1;
            }
            else
            {
                if (i != (pre ^ 1))
                    qmin(low[x], dfn[to[i]]);
            }
        }
    }
    void dfs1(int x)
    {
        block[x] = qwq;
        for (int i = po[x]; i; i = ne[i])
            if (!bridge[i] && !block[to[i]])
            {
                dfs1(to[i]);
            }
    }
    void work()
    {
        ans = 1e18;
        t.init();
        for (int i = 1; i <= n; i++)
            if (!dfn[i])
            {
                dfs(i, 0);
            }
        for (int i = 1; i <= n; i++)
        {
            if (!block[i])
            {
                ++qwq;
                dfs1(i);
            }
        }
        for (int i = 2; i <= id; i += 2)
        {
            sum += w[i];
            if (!bridge[i])
                sze[block[to[i]]]++; // 某个 SSC 边总数
        }
        for (int i = 2; i <= id; i += 2)
        {
            if (block[to[i]] == block[to[i ^ 1]])
                continue;
            if (!bridge[i])
                continue;
            t.add(block[to[i]], block[to[i ^ 1]], w[i]);
            t.add(block[to[i ^ 1]], block[to[i]], w[i]);
        }
        t.work();
        ll ans1 = sum - ans;
        for (int i = 2; i <= id; i += 2) //假设删掉每个边
            if (w[i] < ans && (!bridge[i]))
                ans = w[i];
        sum -= ans;
        printf("%lld\n", sum);
    }
} // namespace SSC
signed main()
{
#ifdef ylx
    freopen("H.in", "r", stdin);
    freopen("H.out", "w", stdout);
#endif
    n = read(), m = read();
    SSC::id = 1;
    ll sum = 0;
    for (int i = 1, x, y, z; i <= m; i++)
    {
        x = read(), y = read(), z = read();
        SSC::add(x, y, z);
        SSC::add(y, x, z);
        sum += z;
    }
    if (m % 2 == 0)
    {
        printf("%lld\n", sum);
        return 0;
    }
    SSC::work();
    return 0;
}
```



### L. Perfect Matchings

#### 题意

在一个 $ 2n(2\le n\le 2000)$ 个点的完全图中，删除一棵 $2n-1$ 条边构成的树（删边），求这个图完美匹配的方案数（完美匹配指选取 $n$ 条边，每条边互不相连）。

#### 做法

dp+ 容斥，设 $dp_{i,j,0/1}$ 表示以 $i$ 为根的子树中，选恰好 $j$ 条树边，其中点 $i$ 使用/没有使用的方案数。dp 转移的时候注意 $j=0$ 时需要特殊考虑。

设 $f_i$ 表示在 $2i$ 个点的完全图上， 选取 $i$ 条边的方案树。

$f_i={2i\choose 2}\times{2i-2\choose 2}\times{2i-4\choose 2}\times\dots {2\choose 2}$

即 $f_i=\frac{(2i)!}{i!\times 2^i}$

最后的总答案为：

$$
\sum \_{i=0}^n (-1)^i \times f\_i \times (dp\_{1,i,0}+dp\_{1,i,1})
$$

注意：菊花图答案为 0 （因为无法完美匹配）。

#### 代码

```cpp
#include <bits/stdc++.h>
#define ll long long
using namespace std;
const int maxn = 4e3 + 100;
const ll mod = 998244353;
int n;
ll ksm(ll x, int y)
{
    ll s = 1;
    while (y)
    {
        if (y & 1) s = s * x % mod;
        x = x * x % mod;
        y >>= 1;
    }
    return s;
}
ll jie[maxn];
ll dp[maxn][2020][2], ans;
int sz[maxn];
int po[maxn], to[maxn << 1], ne[maxn << 1], id;
int rd[maxn];
ll f(int x)
{
    return jie[x * 2] * ksm(ksm(2, x), mod - 2) % mod * ksm(jie[x], mod - 2) % mod;
}
void add(int x, int y)
{
    id++;
    to[id] = y, ne[id] = po[x], po[x] = id;
}
void dfs(int x, int fa)
{
    dp[x][0][0] = 1;
    sz[x] = 1;
    for (int i = po[x]; i; i = ne[i])
        if (to[i] != fa)
        {
            dfs(to[i], x);
            for (int j = sz[x] / 2; j >= 0; j--)
                for (int k = sz[to[i]] / 2; k >= 0; k--)
                {
                    // x 与 to[i] 匹配
                    dp[x][j + k + 1][1] += dp[x][j][0] * dp[to[i]][k][0];
                    // 不用 x
                    if (k)
                    {
                        dp[x][j + k][0] += dp[x][j][0] * dp[to[i]][k][1];
                        dp[x][j + k][0] %= mod;
                        dp[x][j + k][0] += dp[x][j][0] * dp[to[i]][k][0];
                        // 用 x
                        dp[x][j + k][1] += dp[x][j][1] * dp[to[i]][k][1];
                        dp[x][j + k][1] %= mod;
                        dp[x][j + k][1] += dp[x][j][1] * dp[to[i]][k][0];
                    }
                    dp[x][j + k][1] %= mod, dp[x][j + k][0] %= mod;
                    dp[x][j + k + 1][1] %= mod;
                }
            sz[x] += sz[to[i]];
        }
}
int main()
{
#ifdef ylx
    freopen("L.in", "r", stdin);
    freopen("L.out", "w", stdout);
#endif
    n = read();
    n *= 2;
    jie[0] = 1;
    for (int i = 1; i <= n; i++)
    {
        jie[i] = (ll)jie[i - 1] * i % mod;
    }
    for (int i = 1, x, y; i < n; i++)
    {
        x = read(), y = read();
        rd[x]++, rd[y]++;
        add(x, y);
        add(y, x);
    }
    for (int i = 1; i <= n; i++)
        if (rd[i] == n - 1)
        {
            cout << 0;
            return 0;
        }
    dfs(1, 0);
    int m = n / 2;
    for (int i = 0, qwq; i <= m; i++)
    {
        if (i & 1)
            qwq = -1;
        else
            qwq = 1;
        ans += qwq * (dp[1][i][0] + dp[1][i][1]) * f(m - i) % mod;
        ans %= mod;
    }
    if (ans < 0)
        ans += mod;
    printf("%lld\n", ans);
    return 0;
}
```



## The 2021 CCPC Guilin Onsite

### B. A Plus B Problem

#### 题意

给你两个个字符串 $a,b$，长度为 $n$，然后做竖式加法，只保留最后 $n$ 位。

有 $q$ 次修改，每次把第 $x$ 位改成 $y$，求：最后结果的第 $x$ 位是什么，竖式的三行一共有多少个数位变化了。

#### 做法

用线段树维护最后的结果，修改的时候考虑进位和退位。

进位：`x9999999999y+z->[x+1]0000000000[(y+z)%10]`，退位则是反过来。

于是对于每一个区间我们需要维护该区间中最靠左的 9 的位置，最靠左的 0 的位置，该区间是不是全部为 0/9。通过在线段树上二分即可查到可以进位/退位到哪里。

#### 代码

代码中除开 $x=y$ 的时候，有关 t 的维护都是废的。

```cpp
#include <bits/stdc++.h>
using namespace std;
const int maxn = 1e6 + 10;
int n, q;
char s[maxn];
int a[maxn], b[maxn], c[maxn];
int t[maxn << 2];
int p9[maxn << 2]; //如果这个区间全都是9，
int p0[maxn << 2]; //如果这个区间全都是0，则是n+1，否则是这个区间不是0的数的最左的位置
int lazy[maxn << 2];
int ans;
void pushup(int d)
{
    p0[d] = min(p0[d << 1], p0[d << 1 | 1]);
    p9[d] = min(p9[d << 1], p9[d << 1 | 1]);
    t[d] = t[d << 1 | 1];
}
void bt(int x, int y, int d)
{
    lazy[d] = -1; //表示不修改
    if (x == y)
    {
        t[d] = c[x];
        if (t[d] == 0) p0[d] = n + 1, p9[d] = x;
        else if (t[d] == 9) p0[d] = x, p9[d] = n + 1;
        else p0[d] = p9[d] = x;
        return;
    }
    int mid = (x + y) >> 1;
    bt(x, mid, d << 1);
    bt(mid + 1, y, d << 1 | 1);
    pushup(d);
}
void pushdown(int x, int y, int d)
{
    if (x == y || lazy[d] == -1) return;
    int mid = (x + y) >> 1;
    lazy[d << 1] = lazy[d];
    lazy[d << 1 | 1] = lazy[d];
    t[d << 1] = t[d << 1 | 1] = lazy[d];
    for (int i = 0; i <= 1; i++)
    {
        if (lazy[d] == 0)
            p0[d << 1 | i] = n + 1, p9[d << 1 | i] = i == 0 ? x : mid + 1;
        else if (lazy[d] == 9)
            p0[d << 1 | i] = i == 0 ? x : mid + 1, p9[d << 1 | i] = n + 1;
        else
            p0[d << 1 | i] = p9[d << 1 | i] = i == 0 ? x : mid + 1;
    }
    lazy[d] = -1;
}

int query(int x, int y, int d, int p) // 查询第p位是什么
{
    pushdown(x, y, d);
    if (x == y) return t[d];
    int mid = (x + y) >> 1;
    if (p <= mid) return query(x, mid, d << 1, p);
    else return query(mid + 1, y, d << 1 | 1, p);
}
void xg(int x, int y, int d, int l, int r, int z)
{
    pushdown(x, y, d);
    if (l <= x && y <= r)
    {
        t[d] = z;
        if (t[d] == 0) p0[d] = n + 1, p9[d] = x;
        else if (t[d] == 9) p0[d] = x, p9[d] = n + 1;
        else p0[d] = p9[d] = x;
        lazy[d] = z;
        return;
    }
    int mid = (x + y) >> 1;
    if (l <= mid)
        xg(x, mid, d << 1, l, r, z);
    if (r > mid)
        xg(mid + 1, y, d << 1 | 1, l, r, z);
    pushup(d);
}
int query9(int x, int y, int d, int p) // 查询以p开始第1个不是9的在哪
{
    pushdown(x, y, d);
    if (x == y) return x;
    int mid = (x + y) >> 1;
    if (p9[d << 1 | 1] <= p)
        return query9(mid + 1, y, d << 1 | 1, p);
    if (p9[d << 1] <= p)
        return query9(x, mid, d << 1, p);
    return 1;
}
int query0(int x, int y, int d, int p) // p开始第一个不是0的在哪
{
    pushdown(x, y, d);
    if (x == y) return x;
    int mid = (x + y) >> 1;
    if (p0[d << 1 | 1] <= p)
        return query0(mid + 1, y, d << 1 | 1, p);
    if (p0[d << 1] <= p)
        return query0(x, mid, d << 1, p);
    return 1;
}
void add(int p, int x) // 加法
{
    int now = query(1, n, 1, p);
    if (now + x <= 9)
    {
        xg(1, n, 1, p, p, now + x);
        ans++;
        return;
    }
    int pos = query9(1, n, 1, p - 1); // 查询可以进位到哪里
    int kpos = query(1, n, 1, pos);
    xg(1, n, 1, p, p, (now + x) % 10), ans++;
    xg(1, n, 1, pos, pos, kpos + 1); // 这个线段树只有区间赋值的操作
    if (pos != 1) ans++;
    if (pos + 1 <= p - 1)
        xg(1, n, 1, pos + 1, p - 1, 0), ans += (p - 1) - (pos + 1) + 1;
}
void dec(int p, int x) // 减法
{
    int now = query(1, n, 1, p);
    if (now - x >= 0)
    {
        xg(1, n, 1, p, p, now - x);
        ans++;
        return;
    }
    int pos = query0(1, n, 1, p - 1);
    int kpos = query(1, n, 1, pos);
    xg(1, n, 1, p, p, (now - x + 10) % 10), ans++;
    xg(1, n, 1, pos, pos, kpos - 1);
    if (pos != 1) ans++;
    if (pos + 1 <= p - 1)
        xg(1, n, 1, pos + 1, p - 1, 9), ans += (p - 1) - (pos + 1) + 1;
}
int main()
{
    scanf("%d%d", &n, &q);
    scanf("%s", s + 1);
    for (int i = 1; i <= n; i++) a[i + 1] = s[i] - '0';
    scanf("%s", s + 1);
    for (int i = 1; i <= n; i++) b[i + 1] = s[i] - '0';
    n++;
    for (int i = n; i >= 2; i--)
    {
        c[i] += (a[i] + b[i]);
        c[i - 1] += c[i] / 10;
        c[i] %= 10;
    }
    bt(1, n, 1);
    int x, y, z;
    while (q--)
    {
        scanf("%d%d%d", &x, &y, &z);
        y++;
        ans = 0;
        if (x == 1)
        {
            if (a[y] < z)
            {
                add(y, z - a[y]);
                ans++;
                a[y] = z;
            }
            else if (z < a[y])
            {
                dec(y, a[y] - z);
                ans++;
                a[y] = z;
            }
        }
        else
        {
            if (b[y] < z)
            {
                add(y, z - b[y]);
                ans++;
                b[y] = z;
            }
            else if (z < b[y])
            {
                dec(y, b[y] - z);
                ans++;
                b[y] = z;
            }
        }
        printf("%d %d\n", query(1, n, 1, y), ans);
    }
    return 0;
}
```

## 洛谷 P6175 无向图的最小环问题

### 题意

[传送门](https://www.luogu.com.cn/problem/P6175)

给定一张无向图，求图中一个至少包含 $3$ 个点的环，环上的节点不重复，并且环上的边的长度之和最小。该问题称为无向图的最小环问题。在本题中，你需要输出最小的环的边权和。若无解，输出 `No solution.`。

$n\le 100,m\le 10^5$

### 代码

主要是复习 Floyd 求最小环。

注意，环的判断过程，有两条边是原图的边 。

```cpp
const int maxn = 110;
int n, m;
ll f[maxn][maxn], a[maxn][maxn];
int main()
{
#ifdef ylx
    freopen("p6175.in", "r", stdin);
    freopen("p6175.out", "w", stdout);
#endif
    n = read(), m = read();
    memset(f, 1, sizeof(f));
    memset(a, 1, sizeof(a));
    ll z;
    for (int i = 1, x, y; i <= m; i++)
    {
        x = read(), y = read(), z = read();
        if (z < a[x][y])
            a[x][y] = a[y][x] = z, f[x][y] = f[y][x] = z;
    }
    ll ans = f[0][0];
    for (int i = 1; i <= n; i++)
        f[i][i] = 0;
    for (int k = 1; k <= n; k++)
    {
        for (int i = 1; i < k; i++)
        {
            for (int j = i + 1; j < k; j++)
            {
                ans = min(ans, a[i][k] + a[k][j] + f[j][i]);
            }
        }
        for (int i = 1; i <= n; i++)
            for (int j = 1; j <= n; j++)
                f[i][j] = min(f[i][j], f[i][k] + f[k][j]);
    }
    if (ans >= f[0][0])
    {
        cout << "No solution.\n";
    }
    else
        cout << ans;
    return 0;
}
```

