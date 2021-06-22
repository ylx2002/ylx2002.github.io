---

title: Google Code Jam Round 1B 翻车记
slug: 2019GCJ1B
date: 2019-04-29 02:30:00 UTC+08:00
tags: 模拟, 构造, 线段树, 交互, 思维
category: OI
author: yilanxuan
has_math: true

---

因为弱智错误翻车了。1C见。

希望不要再弱智了。

<!-- TEASER_END:  -->

[传送门](https://codingcompetitions.withgoogle.com/codejam/round/0000000000051706)


## A

### 题意

$T$ 组数据，每组数据给 $P$ 个坐标 $(x_i,y_i)$，以及一个方向，表示每个坐标移动的方向。每个坐标将会向固定的方向移动。

需要你返回一组$(x,y)$，满足 $P$ 个坐标尽量多的在往$(x,y)$ 方向移动。（移动后与$(x,y)$ 的距离更近。

若有多个符合条件，输出 $x$ 较小的，若 $x$ 相同，输出 $y$ 较小的一个。

移动规则：

`N` $(x,y)->(x,y+1)$

`S` $(x,y)->(x,y-1)$

`W` $(x,y)->(x-1,y)$

`E` $(x,y)->(x+1,y)$

数据范围：

$T\le 100$，$1\le P\le 500$，$Q\le 10^5$

$0 \le Xi \le Q$, for all i.

$0 \le Yi \le Q$, for all i.

时限 $20s$

### 做法

$(x,y)$ 两个维度互不影响。

然后一个维度一个维度的考虑，有效状态只有 $2P$ 个。（$x\_i$ 或者 $x\_i+1$），于是时间复杂度 $\sum O(P)
$

$P^2$ 也可以过。

### 代码

```cpp
const int maxn = 510;
int T, P, Q;
struct node
{
    int x, y, ty;
} a[maxn];
bool cmp(node aa, node bb)
{
    return aa.x < bb.x;
}
bool cmp1(node aa, node bb)
{
    return aa.y < bb.y;
}
int main()
{
    T = read();
    char ch;
    int Tt = 0;
    while (T--)
    {
        Tt++;
        P = read(), Q = read();
        for (int i = 1; i <= P; i++)
        {
            a[i].x = read(), a[i].y = read();
            ch = getchar();
            while (ch != 'N' && ch != 'S' && ch != 'W' && ch != 'E')
                ch = getchar();
            if (ch == 'N')
                a[i].ty = 0;
            else if (ch == 'S')
                a[i].ty = 1;
            else if (ch == 'W')
                a[i].ty = 2;
            else
                a[i].ty = 3;
        }
        int cnt = 0, ansx = 1e9, ansy = 1e9;
        int Max = 0;
        sort(a + 1, a + P + 1, cmp);
        for (int i = 1; i <= P; i++)
            if (a[i].ty == 2 && a[i].x != 0)
                cnt++;
        if (cnt > Max)
        {
            Max = cnt;
            ansx = 0;
        }
        for (int i = 1; i <= P; i++)
        {
            cnt = 0;
            for (int j = 1; j <= i; j++)
                if (a[j].ty == 3 && a[j].x < a[i].x)
                    cnt++;
            for (int j = i + 1; j <= P; j++)
                if (a[j].ty == 2 && a[j].x > a[i].x)
                    cnt++;
            if (cnt > Max)
                Max = cnt, ansx = a[i].x;
            cnt = 0;
            for (int j = 1; j <= i; j++)
                if (a[j].ty == 3)
                    cnt++;
            for (int j = i + 1; j <= P; j++)
                if (a[j].ty == 2 && a[j].x > a[i].x + 1)
                    cnt++;
            if (cnt > Max && a[i].x + 1 <= Q)
            {
                Max = cnt;
                ansx = a[i].x + 1;
            }
        }
        if (Max == 0||ansx>Q)
            ansx = 0;
        cnt = 0;
        Max = 0;
        sort(a + 1, a + P + 1, cmp1);
        for (int i = 1; i <= P; i++)
            if (a[i].ty == 1 && a[i].y != 0)
                cnt++;
        if (cnt > Max)
        {
            Max = cnt;
            ansy = 0;
        }
        for (int i = 1; i <= P; i++)
        {

            cnt = 0;
            for (int j = 1; j <= i; j++)
                if (a[j].ty == 0 && a[j].y < a[i].y)
                    cnt++;
            for (int j = i + 1; j <= P; j++)
                if (a[j].ty == 1 && a[j].y > a[i].y)
                    cnt++;
            if (cnt > Max)
            {
                Max = cnt;
                ansy = a[i].y;
            }

            cnt = 0;
            for (int j = 1; j <= i; j++)
                if (a[j].ty == 0)
                    cnt++;
            for (int j = i + 1; j <= P; j++)
                if (a[j].ty == 1 && a[j].y > a[i].y + 1)
                    cnt++;
            if (cnt > Max && a[i].y + 1 <= Q)
            {
                Max = cnt;
                ansy = a[i].y + 1;
            }
        }
        if (Max == 0 || ansy > Q)
            ansy = 0;
        printf("Case #%d: %d %d\n", Tt, ansx, ansy);
    }
    return 0;
}
```

## B

### 题意

交互题。

你有 $6$ 种物品，每种物品有 $x_i$ 个，第 $i$ 种物品有每 $i$ 天会翻倍。

你可以询问 $W$ 次，每次询问询问第 $x$ 天物品的总数$\mod 2^{63}$。

求第 $0$ 天每种物品的数量。 $1\le x\le 500$

subtask1：$W=6$，subtask2：$W=2$。

$T\le 50$

时限：$20s$

### 做法

$W=6$ 的时候可以直接差分。

物品 $i$ 第 $j$ 天的系数为 $2^{\lfloor\frac{j}{i}\rfloor}$

询问第 $i$ 天得到的是：

$$
\sum \_{j=1} ^ 6 2^{\lfloor\frac{i}{j}\rfloor} a\_i\mod 2^{63}
$$ 

相当于 $a_i$ 前面乘了个系数，然后我们对这个系数取对数打表。

（后面的数表示系数）

`x=210 , 0  0  0 52 42 35` 

因为 $0\le x_i\le 100$，所以可以通过除若干个2，并且不会影响前面的，求出 $x_4,x_5,x_6$。

比如除 $42$ 次 $2$ 得到的是 $2^{10}\times x_4+x_5$，$x_6$被向下取整这种东西吃掉了。  

`x=44 , 44 22 14 11 8 7`

代入$x\_4,x\_5,x\_6$，同样的方式求出$x\_1,x\_2,x\_3$。

### 代码

```cpp
ll s[7], a[7], ss;
int T, W;
int main()
{
    scanf("%d%d", &T, &W);
    while (T--)
    {
        printf("%d\n", 210);
        fflush(stdout);
        scanf("%lld", &s[0]);
        printf("%d\n", 44);
        fflush(stdout);
        scanf("%lld", &s[1]);
        ss = s[0];
        for (int i = 1; i <= 52; i++)
            ss /= 2;
        a[4] = ss;

        ss = 1;
        for (int i = 1; i <= 52; i++)
            ss = ss * 2;
        ss *= a[4];
        s[3] = s[0] - ss; //去了a[4] 求 a[5]
        ss = s[3];
        for (int i = 1; i <= 42; i++)
            ss /= 2;
        a[5] = ss;

        ss = 1;
        for (int i = 1; i <= 42; i++)
            ss = ss * 2;
        ss *= a[5];
        ss = s[3] - ss; //去了a[4],a[5] 求 a[6]
        for (int i = 1; i <= 35; i++)
            ss /= 2;
        a[6] = ss;
        //------------------------------------
        s[2] = s[1];
        ss = 1;
        for (int i = 1; i <= 7; i++)
            ss = ss * 2;
        ss *= a[6];
        s[2] -= ss;

        ss = 1;
        for (int i = 1; i <= 8; i++)
            ss = ss * 2;
        ss *= a[5];
        s[2] -= ss;

        ss = 1;
        for (int i = 1; i <= 11; i++)
            ss = ss * 2;
        ss *= a[4];
        s[2] -= ss; //减去a[4],a[5],a[6]
        //------------------------------------
        ss = s[2];
        for (int i = 1; i <= 44; i++)
            ss /= 2;
        a[1] = ss;
        ss = 1;
        for (int i = 1; i <= 44; i++)
            ss = ss * 2;
        ss *= a[1];

        s[3] = s[2] - ss; //去掉a[1]
        ss = s[3];
        for (int i = 1; i <= 22; i++)
            ss /= 2;
        a[2] = ss;

        ss = 1;
        for (int i = 1; i <= 22; i++)
            ss *= 2;
        ss *= a[2];
        s[3] -= ss; //去掉a[2]
        ss = s[3];
        for (int i = 1; i <= 14; i++)
            ss /= 2;
        a[3] = ss;

        for (int i = 1; i <= 6; i++)
            printf("%d ", a[i]);
        printf("\n");
        fflush(stdout);
        int X;
        scanf("%d", &X);
        if (X == -1)
            return 0;
    }
    return 0;
}
```

## C

### 题意

第一行： $N,K$

第二行：共 $N$ 个数，$C_i$

第三行：共 $N$ 个数，$D_i$

求有多少对 $(x,y)$ 满足

$$
\Big|\max \_{i=x}^y C\_i-\max\_{i=x}^y D\_i\Big|\le K
$$

数据范围：

Time limit: 30 seconds per test set.

Memory limit: 1GB.

$T\le 100$ 

Test set 1 (Visible)

$1 \le N \le 100$.

Test set 2 (Hidden)

$N = 10^5$, for exactly 8 test cases.

$1 \le N \le 1000$, for all but 8 test cases.


### 做法

可以发现，固定 $r$ 的 时候，所有的 $l$ 到 $r$ 的 $\max c\_i,\max d\_i$是单调的。

对于每一个 $c_i$ ,合法的 $d_i$ 是一个固定的范围。

考虑每次加入一个右端点。

对于每个 $l$ 有三种情况：

$\max c_i,\max d_i$ 都变了（直接修改）。

$\max c_i$ 变了，

合法的 $\max d_i$ 的范围为 $\max c_i-K$ 到 $\max c_i+K$ 这是一个连续的范围。

取并就好。

$\max d_i$ 变了，

同理。

### 代码

```cpp
const int maxn = 1e5 + 10;
int T, n, k;
int t[maxn << 2], tag[maxn << 2];
int a[2][maxn];
void pushdown(int d, int l, int r)
{
    int mid = (l + r) >> 1;
    if (tag[d] != -1)
    {
        t[d << 1] = (mid - l + 1) * tag[d];
        t[d << 1 | 1] = (r - mid) * tag[d];
        tag[d << 1] = tag[d];
        tag[d << 1 | 1] = tag[d];
        tag[d] = -1;
    }
}
void add(int x, int y, int d, int l, int r, int tg)
{
    if (l <= x && y <= r)
    {
        tag[d] = tg;
        t[d] = (y - x + 1) * tg;
        return;
    }
    pushdown(d, x, y);
    int mid = (x + y) >> 1;
    if (l <= mid)
        add(x, mid, d << 1, l, r, tg);
    if (r > mid)
        add(mid + 1, y, d << 1 | 1, l, r, tg);
    t[d] = t[d << 1] + t[d << 1 | 1];
}
int qsum(int x, int y, int d, int l, int r)
{
    if (l <= x && y <= r)
        return t[d];
    if (!t[d])
        return 0;
    int mid = (x + y) >> 1, s = 0;
    pushdown(d, x, y);
    if (l <= mid)
        s += qsum(x, mid, d << 1, l, r);
    if (r > mid)
        s += qsum(mid + 1, y, d << 1 | 1, l, r);
    return s;
}
struct Stack
{
    int top;
    int val[maxn], p[maxn];
} s[2];
int findmax(int x, int ty) //>=x
{
    int l = 1, r = s[ty].top, ans = 0, mid;
    while (l <= r)
    {
        mid = (l + r) >> 1;
        if (s[ty].val[mid] >= x)
            ans = mid, l = mid + 1;
        else
            r = mid - 1;
    }
    if (!ans)
        return 0;
    return s[ty].p[ans];
}
int findmin(int x, int ty) //<=x
{
    int l = 1, r = s[ty].top;
    int ans = 0, mid;
    while (l <= r)
    {
        mid = (l + r) >> 1;
        if (s[ty].val[mid] <= x)
            ans = mid, r = mid - 1;
        else
            l = mid + 1;
    }
    if (!ans)
        return n + 1;
    return s[ty].p[ans - 1] + 1;
}
int main()
{
#ifdef ylx
    freopen("C.in", "r", stdin);
    freopen("C.out", "w", stdout);
#endif
    int TOT = 0;
    T = read();
    while (T--)
    {
        memset(a, 0, sizeof(a));
        s[0].val[1] = s[0].p[1] = 0;
        s[1].val[1] = s[1].p[1] = 0;
        TOT++;
        ll ans = 0;
        n = read(), k = read();
        for (int i = 1; i <= n; i++)
            a[0][i] = read();
        for (int i = 1; i <= n; i++)
            a[1][i] = read();
        memset(t, 0, sizeof(t));
        memset(tag, -1, sizeof(tag));
        s[0].top = 0;
        s[1].top = 0;
        for (int r = 1, L, R, p, l1, r1; r <= n; r++)
        {
            for (int i = 0; i <= 1; i++)
            {
                while (s[i].top && s[i].val[s[i].top] <= a[i][r])
                    s[i].top--;
                s[i].top++;
                s[i].val[s[i].top] = a[i][r];
                s[i].p[s[i].top] = r;
            }
            // case 1
            L = max(s[0].p[s[0].top - 1] + 1, s[1].p[s[1].top - 1] + 1);
            R = r;
            add(1, n, 1, L, R, abs(a[0][r] - a[1][r]) <= k);
            // case 2
            R = L - 1;
            L = min(s[0].p[s[0].top - 1] + 1, s[1].p[s[1].top - 1] + 1);
            p = 1;
            l1 = L, r1 = R;
            if (l1 > r1)
            {
                ans += qsum(1, n, 1, 1, r);
                continue;
            }
            if (L == s[0].p[s[0].top - 1] + 1)
                p = 0;
            L = max(L, findmin(a[p][r] + k, p ^ 1));
            R = min(R, findmax(a[p][r] - k, p ^ 1));
            if (L <= R)
                add(1, n, 1, L, R, 1);
            if (l1 < L)
                add(1, n, 1, l1, L - 1, 0);
            if (R < r1)
                add(1, n, 1, R + 1, r1, 0);
            ans += qsum(1, n, 1, 1, r);
        }
        printf("Case #%d: %lld\n", TOT, ans);
    }
}
```

---

藏在底下的碎碎念。

翻车原因：T3看错题耽误太久了，T1 细节耽误太久了，T2 看错题还行。

英语不好，打扰了。

感觉题目还是比较好的。

就是要翻墙差评。

自己还是不够稳啊，经常开场太着急。

还是要稳住吧。
