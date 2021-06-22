---

title: Google Code Jam Round 1C 翻车记
slug: 2019GCJ1C
date: 2019-05-04 19:30:00 UTC+08:00
tags: 贪心, 交互, 博弈, dp
category: OI
author: yilanxuan
has_math: true

---

因为弱智错误差点翻车了。

希望不要再弱智了。

<!-- TEASER_END:  -->

[传送门](https://codingcompetitions.withgoogle.com/codejam/round/00000000000516b9)

## A

### 题意


有 A 个机器人和你玩石头剪刀布，每个机器人出的序列是固定的，循环的（出完最后一个后出第一个），R 石头，P 布，S 剪刀。

每次和一个机器人玩，如果双方出的相同，则进行下一轮，直到分出胜负。

需要你给出一个固定的序列，也是循环的。要求要赢所有的机器人。

否则输出 IMPOSSIBLE。

Limits

1 ≤ T ≤ 100.

Time limit: 20 seconds per test set.

Memory limit: 1GB.

Each character in Ci is uppercase R, P, or S, for all i.

A = 2K - 1 for some integer K ≥ 1.

Test set 1 (Visible)

1 ≤ A ≤ 7.

Ci is between 1 and 5 characters long, for all i.

Test set 2 (Hidden)

1 ≤ A ≤ 255.

Ci is between 1 and 500 characters long, for all i.

Input

```
3
1
RS
3
R
P
S
7
RS
RS
RS
RS
RS
RS
RS
```

Output

```
Case #1: RSRSRSP
Case #2: IMPOSSIBLE
Case #3: P
```
### 做法

开始看错题了。

可以发现，自己的出牌序列是不会被循环的。（如果可以赢的话）

如果某个位置机器人出现了出的方式，那么就 IMPOSSIBLE ，

否则机器人一定只有一种或两种出的方式。

如果是一种可以直接赢所有的机器人，

如果是两种的话，假设这两种是 A 和 B，A 可以打败 B，那么就出 A ，然后删掉所有输掉的机器人，回到上面个步骤。

因为如果出 A 的话，那么当前出 B 的机器人就赢了。

不被循环的这个条件骗了就好。

复杂度 $O(n^2)$

### 代码

```cpp
const int N = 510;
const int maxn = 255 + 10;
int T, n, p[maxn], m, l[maxn],ct;
char ch[maxn][N];
char ans[N];
int cnt[500],win[500];
int main()
{
    int tot = 0;
    T = read();
    while (T--)
    {
        ++tot;
        printf("Case #%d: ", tot);
        n = read();
        m = 0;
        ct = 0;
        for (int i = 1; i <= n; i++)
        {
            scanf("%s", ch[i] + 1);
            p[i] = 1;
            l[i] = strlen(ch[i] + 1);
            qmax(m, l[i]);
            win[i] = 0;
        }
        for (int i = 1;; i++)
        {
            cnt['R'] = 0;
            cnt['P'] = 0;
            cnt['S'] = 0;
            if (i > 500)
            {
                ct = -1;
                break;
            }
            for (int j = 1; j <= n; j++)
                if (!win[j])
                    cnt[ch[j][p[j]]]++;
            if (cnt['R'] && cnt['P'] && cnt['S'])
            {
                ct = -1;
                break;
            }
            if (cnt['R'] && cnt['P'])
            {
                ct++;
                ans[ct] = 'P';
                for (int j = 1; j <= n; j++)
                    if (ch[j][p[j]] == 'R')
                    {
                        win[j] = 1;
                    }
            }
            else if (cnt['R'] && cnt['S'])
            {
                ct++;
                ans[ct] = 'R';
                for (int j = 1; j <= n; j++)
                    if (ch[j][p[j]] == 'S')
                    {
                        win[j] = 1;
                    }
            }
            else if (cnt['P'] && cnt['S'])
            {
                ct++;
                ans[ct] = 'S';
                for (int j = 1; j <= n; j++)
                    if (ch[j][p[j]] == 'P')
                    {
                        win[j] = 1;
                    }
            }
            else
            {
                ct++;
                if (cnt['P'])
                    ans[ct] = 'S';
                else if (cnt['R'])
                    ans[ct] = 'P';
                else
                    ans[ct] = 'R';
                break;
            }
            for (int j = 1; j <= n; j++)
                if (!win[j])
                {
                    p[j]++;
                    if (p[j] > l[j])
                        p[j] = 1;
                }
        }
        if (ct == -1)
        {
            printf("IMPOSSIBLE");
        }
        else
        {
            for (int i = 1; i <= ct; i++)
                putchar(ans[i]);
        }

        putchar('\n');
    }
}
```

## B

### 题意

A~E 的排列共 120 个，现在丢了一种。

你有 $F$ 次询问，每次询问一个数 x 表示第 `x/5+1` 个排列的第 `(x-1)%5+1` 个位置的字母。

要求返回丢失的那个排列。

Limits

1 ≤ T ≤ 50.

Time limit: 40 seconds per test set.

Memory limit: 1GB.

The missing set, and the order of the remaining sets, are chosen uniformly and independently at random.

Test set 1 (Visible)

F = 475.

Test set 2 (Hidden)

F = 150.

### 做法

询问出第一位需要 119 次，

然后我们可以知道丢失的排列的第一位是什么的，

第二次询问就只要询问第一位为丢失的排列的第一位的。

询问第二位需要 23 次，询问第三位需要 5 次，询问第四位需要 1 次。

一共询问 148 次。

### 代码

```cpp
int T, F;
char ch;
int vis[130], g[120];
int cnt[10], ans[10],use[10];
char s[10];
int main()
{
    scanf("%d%d", &T, &F);
    while (T--)
    {
        memset(vis, 0, sizeof(vis));
        memset(use, 0, sizeof(use));
        for (int j = 1; j <= 5; j++)
        {
            cnt['A' - 'A'] = 0;
            cnt['B' - 'A'] = 0;
            cnt['C' - 'A'] = 0;
            cnt['D' - 'A'] = 0;
            cnt['E' - 'A'] = 0;
            for (int i = 0; i < 119; i++)
            {
                if (vis[i] == j - 1)
                {
                    printf("%d\n", i * 5 + j);
                    fflush(stdout);
                    scanf("%s", s);
                    ch = s[0];
                    g[i] = ch - 'A';
                    cnt[ch - 'A']++;
                }
            }
            int Min = -1;
            for (int i = 0; i <= 4; i++)
            {
                if ((Min == -1 || cnt[i] < cnt[Min]) && !use[i])
                    Min = i;
            }
            ans[j] = Min;
            use[Min] = 1;
            for (int i = 0; i < 119; i++)
            {
                if (g[i] == Min && vis[i] == j - 1)
                    vis[i] = j;
            }
        }
        for (int i = 1; i <= 5; i++)
            printf("%c", ans[i] + 'A');
        printf("\n");
        fflush(stdout);
        scanf("%s", s);
        if (s[0] == 'N')
            return 0;
    }
}
```

## C

### 题意

给你一个 $R*C$ 的矩阵，其中有一些位置是被污染的。

两个人轮流操作，B先手，T后手。

每次操作：选则一个位置放 H 细菌或者 V 细菌，如果是H左右扩展，如果是V就上下扩展，直到无法扩展。

如果接触到了被污染的位置就输了。

如果B输了输出0，否则输出B第一步有多少种放的方案可以赢。

Limits

Time limit: 30 seconds per test set.

Memory limit: 1GB.

1 ≤ T ≤ 100.

Test set 1 (Visible)

1 ≤ R ≤ 4.

1 ≤ C ≤ 4.

Test set 2 (Hidden)

1 ≤ R ≤ 15.

1 ≤ C ≤ 15.

### 做法

sg函数可以判断出是否为必胜态。

如果是必胜态的话，能转移到多少个 sg 值为0的位置，第一步有多少种放的方案可以赢。（注意每一行可以扩展到的位置都是可以作为第一次放的位置的。

然后没什么细节了吧，比较裸。

~~1B好玩一些。~~

### 代码

```cpp
#define x1 _
#define x2 __
#define y1 ___
#define y2 ____
const int maxn = 30;
int T, tt, ans;
int n, m;
char c[maxn][maxn];
int f[16][16][16][16];
int dfs(int x1, int y1, int x2, int y2)
{
    if (f[x1][y1][x2][y2] >= 0)
        return f[x1][y1][x2][y2];
    int z[40] = {0};
    for (int i = x1, flag, sg; i <= x2; i++) //删行
    {
        flag = 1;
        for (int j = y1; j <= y2; j++)
            if (c[i][j] == '#')
                flag = 0;
        if (flag)
        {
            sg = 0;
            if (i - 1 >= x1)
                sg ^= dfs(x1, y1, i - 1, y2);
            if (i + 1 <= x2)
                sg ^= dfs(i + 1, y1, x2, y2);
            z[sg] += y2 - y1 + 1;
        }
    }

    for (int i = y1, flag, sg; i <= y2; i++) //删列
    {
        flag = 1;
        for (int j = x1; j <= x2; j++)
            if (c[j][i] == '#')
            {
                flag = 0;
                break;
            }
        if (flag)
        {
            sg = 0;
            if (i - 1 >= y1)
                sg ^= dfs(x1, y1, x2, i - 1);
            if (i + 1 <= y2)
                sg ^= dfs(x1, i + 1, x2, y2);
            z[sg] += x2 - x1 + 1;
        }
    }

    int p = 0;
    while (z[p])
        ++p;
    f[x1][y1][x2][y2] = p;
    if (x1 == 1 && y1 == 1 && x2 == n && y2 == m)
        ans = z[0];
    return f[x1][y1][x2][y2];
}
int main()
{
    T = read();
    while (T--)
    {
        tt++;
        n = read(), m = read();
        for (int i = 1; i <= n; i++)
            scanf("%s", c[i] + 1);
        for (int x1 = 1; x1 <= n; x1++)
            for (int x2 = 1; x2 <= n; x2++)
                for (int y1 = 1; y1 <= m; y1++)
                    for (int y2 = 1; y2 <= m; y2++)
                        f[x1][y1][x2][y2] = -1;
        ans = 0;
        dfs(1, 1, n, m);
        printf("Case #%d: %d\n", tt, ans);
    }
}
```

---

感谢noname小鸽鸽教我测T2+看出一个我傻逼错误+提供T1,T2题意。
