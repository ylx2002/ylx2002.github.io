---

title: GYM 102082 乱做
slug: gym102082
date: 2019-04-14 13:00:00 UTC+08:00
tags: dp, 构造, 贪心
category: OI 
author: yilanxuan
has_math: true

---

两个人和一只鸽子一起开的vp。

<!-- TEASER_END:  -->

gym 102082  2018-2019, ICPC, Asia Yokohama Regional Contest 2018 乱做

EFHI没补。

tag : 思维，dp，贪心，bfs，构造

## C

### 题意

你有一台车，有 $r\*s\*2$ 个座位，$1\le r,s\le 500$

有 $p$ 个人，给出每个人的位置，每秒每个人可以同时移动一步，但是每个位置同时只能有一个人。

求最早什么时候所有人离开车。（图什么的看题面吧）

### 做法

大概就是装作每个位置可以堆很多人，但是每次只能出去一个，这样不会影响答案。

所以我们让每个人以最快的速度到达距离出口为1的位置，然后暴力开桶记哪个时间有人出去，就可以求出答案了。

当然还有一种做法，可以拓展到树上。

就是set启发式合并时间区间了，~~想要复杂度更高的话可以选择bitset合并~~

set维护连续区间。

然后wa了几次，因为数组开小了，开成了$500+500*2+1$。

### 代码
```cpp
const int maxm = 500 * 500 * 2 + 10;
int n, m, p;
int a[maxm], id;
int main()
{
#ifdef ylx
    freopen("C.in", "r", stdin);
    freopen("C.out", "w", stdout);
#endif
    n = read(), m = read(), p = read();
    for (int i = 1, x, y; i <= p; i++)
    {
        x = read(), y = read();
        if (y > m)
            y++;
        a[i] = abs(y - (m + 1)) + abs((n + 1) - x);
    }
    sort(a + 1, a + p + 1);
    id = 0;
    for (int i = 1; i <= p; i++)
    {
        if (a[i] > id)
            id = a[i];
        else
            ++id;
    }
    cout << id << endl;
    return 0;
}
```

## D

### 题意

给你两个01字符串，要你构造一个01字符串，使这个字符串不为任意一个给定的字符串的子序列。

$|s_1|,|s_2|\le 5\times 10^3$

答案不唯一的时候输出**字典序最小**的一个。

### 做法

预处理出每个串每个位置下一个$0,1$的位置。

直接$dp_{i,j}$表示构造的串在第一个串中**最靠前**匹配子序列到$i$位，在第二个串中**最靠前**匹配子序列到$j$位，然后记方案。(不知道怎么表达，看代码。)

可是保证不了字典序最小。

于是我们可以把$dp$数组建成一个$dag$，然后用bfs转移，先转移0再转移1,这样我们最后倒着输出方案的时候就是字典序最小的方案了。（~~递归输出怕不是会被卡栈~~）

### 代码

```cpp
const int maxn = 4e3 + 2;
int a[maxn], b[maxn];
short dp[maxn][maxn];
short pre[maxn][maxn][2];
bitset<maxn> c[maxn];
int n, m;
char ch[maxn];
int A[maxn][2], B[maxn][2];
int po[2];
void init()
{
    po[0] = po[1] = n + 1;
    A[n + 1][0] = A[n + 1][1] = n + 1;
    for (int i = n; i >= 0; i--)
    {
        A[i][a[i]] = po[a[i]];
        A[i][a[i] ^ 1] = po[a[i] ^ 1];
        po[a[i]] = i;
    }
    po[0] = po[1] = m + 1;
    B[m + 1][0] = B[m + 1][1] = m + 1;
    for (int i = m; i >= 0; i--)
    {
        B[i][b[i]] = po[b[i]];
        B[i][b[i] ^ 1] = po[b[i] ^ 1];
        po[b[i]] = i;
    }
}
void write(int x, int y)
{
    if (!x && !y)
    {
        return;
    }
    write(pre[x][y][0], pre[x][y][1]);
    if (c[x][y])
    {
        putchar('1');
    }
    else
    {
        putchar('0');
    }
}
#define pr pair<short, short>
queue<pr> q;
pr z;
int main()
{
#ifdef ylx
    freopen("D.in", "r", stdin);
    freopen("D.out", "w", stdout);
#endif
    scanf("%s", ch + 1);
    n = strlen(ch + 1);
    for (int i = 1; i <= n; i++)
        a[i] = ch[i] ^ '0';
    scanf("%s", ch + 1);
    m = strlen(ch + 1);
    for (int i = 1; i <= m; i++)
        b[i] = ch[i] ^ '0';
    init();
    memset(dp, 0x3f, sizeof(dp));
    z.first=0;
    z.second=0;
    q.push(z);
    dp[0][0] = 0;
    int inf = dp[1][1],i,j,x,y;
    while (!q.empty())
    {
        z = q.front();
        q.pop();
        i = z.first;
        j = z.second;
        //add(0);
        x = A[i][0];
        y = B[j][0];
        if (dp[i][j] + 1 < dp[x][y])
        {
            pre[x][y][0] = i;
            pre[x][y][1] = j;
            dp[x][y] = dp[i][j] + 1;
            z.first = x, z.second = y, q.push(z);
        }
        //add(1);
        x = A[i][1];
        y = B[j][1];
        if (dp[i][j] + 1 < dp[x][y])
        {
            pre[x][y][0] = i;
            pre[x][y][1] = j;
            c[x][y] = 1;
            dp[x][y] = dp[i][j] + 1;
            z.first = x, z.second = y, q.push(z);
        }
    }
    write(n + 1, m + 1);
    return 0;
}
//2019年 04月 14日 星期日 11:41:38 CST
```

## J

### 题意

动态维护虚树，还有路径长度和。

### 做法

用set动态维护虚树(dfs序），树剖来求各种东西。

### 代码

~~不是我写的，告辞。~~

## K

### 题意

两个人玩$n$轮游戏，每次两个人各取一个数，并且已知对手的取数顺序，如果你取的数比他大就算赢一轮，求能赢最多轮次的取法，如果有多种取法输出**字典序最大**的取法。

输入：第一行 $n$，第二行 $a$ 数组，第三行 $b$ 数组。

($a$数组是对手的取数顺序。)

### 做法

加个字典序最大，难度直接从`入门难度`到了`普及+\提高`。

在一个假做法上耗费了一天。

其实做法不假，但是很难写

不好写的做法：

字典序最大的话，可以转化一下，相当与尽量小的数尽量放在后面。于是我们可以倒着把这些数安排得明明白白。我们把数组$b$中的数从小到大排序。我们可以快速求出剩下的 $b$ 最大可以匹配多少个 $a$ ，然后从大到小枚举 $b$ 在 $a$ 中的位置，然后用$set$ 维护剩下的是否合法。

这样有两种情况，第一种是记贡献，第二种是不记贡献...

剩下的自行脑补。

好写点的做法：

~~我开始一定是脑子进水了。~~

字典序最大的话，我们可以从头开始枚举每个$a_i$ 对应哪个 $b_i$，
有$3$种情况。

1. 这个位置必须记贡献。
2. 这个位置可以记贡献可以不记贡献，
3. 这个位置不能记贡献。

我们最开始可以求出 $kk$ 表示还要多少个数记录贡献。匹配到$i$位后，若有$a_i< b_i$ ($b_i$是匹配后的数)，则$kk--$。

对于$a_i$，我们删去它后，只能匹配上 $kk-1$ 对的话，$a_i$ 必须记贡献。

如果每个未使用的数 $b_j$ 都比 $a_i$ 小，就必须不记贡献。

否则都可以。

在记贡献和不记贡献的情况下，优先选择记贡献。

具体看代码，我写得挺丑的。

### 代码

```cpp
#define fi first
#define se second
#define mp make_pair
const int maxn = 5050;
struct node
{
	int x, p, use;
} a[maxn], b[maxn], c[maxn];
int z[maxn];
int n, K; //K:合法匹配数
bool cmp(node aa, node bb)
{
	if (aa.use != bb.use)
		return aa.use < bb.use;
	if (aa.x == bb.x)
		return aa.p < bb.p;
	return aa.x < bb.x;
}
int main()
{
#ifdef ylx
	freopen("K.in", "r", stdin);
	freopen("K.out", "w", stdout);
#endif
	n = read();
	for (int i = 1; i <= n; i++)
	{
		a[i].x = read(), a[i].p = i;
		c[i]=a[i];
	}
	for (int i = 1; i <= n; i++)
		b[i].x = read(), b[i].p = i;
	sort(c + 1, c + n + 1, cmp);
	sort(b + 1, b + n + 1, cmp);
	int pp = 1, kk = 0;
	for (int j = 1; j <= n; j++)
	{
		while (pp <= n && b[pp].x <= c[j].x)
			++pp;
		if (pp > n)
			break;
		if (b[pp].x > c[j].x)
		{
			++pp;
			++kk;
		}
	}
	for (int i = 1, k, len, p, flag, f2; i <= n; i++)
	{
		flag = 0;
		f2 = 0;
		len = n - i + 1;
		sort(b + 1, b + n + 1, cmp); //b:1 -> n-i+1
		for (int j = i; j <= n; j++)
			c[j - i + 1] = a[j];
		sort(c + 2, c + len + 1, cmp); //a[i] 不参与排序
		//b 匹配 c
		k = 0, p = 1;
		memset(z, 0, sizeof(z));
		for (int j = 2; j <= len; j++)
		{
			while (p <= len && b[p].x <= c[j].x)
				++p;
			if (p > len || b[p].use)
				break;
			if (b[p].x > c[j].x)
			{
				if (b[p].x > c[1].x)
					f2 = k + 1;
				z[p] = 1;
				++p, ++k;
			}
		}
		if (f2)
			f2 = len - k + f2;
		if (k + 1 == kk)
			f2 = 0;
		for (int j = len; j >= 1; j--)
			if (!z[j] && !b[j].use)
			{
				if (b[j].x > c[1].x)
				{
					flag = j;
					break;
				}
			}
		flag = max(flag, f2);
		if (flag)
		{
			b[flag].use = 1;
			kk--;
			printf("%d ", b[flag].x);
		}
		else
		{
			for (int j = len; j >= 1; j--)
				if (!z[j] && !b[j].use)
				{
					b[j].use = 1;
					printf("%d ", b[j].x);
					break;
				}
		}
	}
	return 0;
}
//2019年 04月 14日 星期日 16:07:10 CST

```
