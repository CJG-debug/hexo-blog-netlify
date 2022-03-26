---
sticky: ""
title: 「Summary」NOI Online 2022 提高组 瞎搞游记
date: 2022-03-26 15:39:55
updated: 2022-03-26 15:39:55
tags:
  - 游记
comments: true
---
真·乱搞   
<!-- more -->

## 丹钓战
### Link
[link](https://www.luogu.com.cn/problem/P8251)
### Sol
没啥好说的。   
先按照题意模拟一遍《丹钓战》，然后你可记录在未插入当前点时栈顶点的下标，作为当前点的权值。   
对于查询 `[l, r]` ，就是找 `[l, r]` 内权值 `<l` 的点的个数。
### Code

<details>

```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
using namespace std;
const int MAXN = 5e5 + 5;
const int MAXLOG = 55;

inline void read(int& x) {
	x = 0; int f = 1;
	char c = getchar();
	while (c < '0' || c > '9') {
		if (c == '-') f = -f;
		c = getchar();
	}
	while (c >= '0' && c <= '9') {
		x = (x << 3) + (x << 1) + (c ^ 48);
		c = getchar();
	}
	x *= f;
}

inline void write(int x) {
	if (x < 0) {
		putchar('-');
		x = -x;
	}
	if (x > 9)
		write(x / 10);
	putchar(x % 10 + '0');
}

int n, m, tp, pre[MAXN];
struct Position { int a, b, ind; } pos[MAXN], st[MAXN];

struct PresidentTree {
	int tot, root[MAXN];
	struct Node { int lch, rch, dat; } s[MAXN * MAXLOG];
	void push_up(int p) { s[p].dat = s[s[p].lch].dat + s[s[p].rch].dat; }
	void update(int& p, int l, int r, int pos, int val) {
		s[++tot] = s[p], p = tot;
		if (l == r) {
			s[p].dat += val;
			return;
		}
		int mid = (l + r) >> 1;
		if (pos <= mid) update(s[p].lch, l, mid, pos, val);
		else update(s[p].rch, mid + 1, r, pos, val);
		push_up(p);
	}
	int query(int p, int l, int r, int ql, int qr) {
		if (l >= ql && r <= qr) return s[p].dat;
		int mid = (l + r) >> 1, res = 0;
		if (ql <= mid) res += query(s[p].lch, l, mid, ql, qr);
		if (qr > mid) res += query(s[p].rch, mid + 1, r, ql, qr);
		return res;
	}
} pret;

int main() {
	
	freopen("stack.in", "r", stdin);
	freopen("stack.out", "w", stdout);

	// scanf("%d %d", &n, &m);
	read(n), read(m);
	for (int i = 1; i <= n; i++) {
		// scanf("%d", &pos[i].a);
		read(pos[i].a);
	}
	for (int i = 1; i <= n; i++) {
		// scanf("%d", &pos[i].b);
		read(pos[i].b);
	}

	// int tot = 0;
	for (int i = 1; i <= n; i++) {
		pos[i].ind = i;
		while (tp && (st[tp].a == pos[i].a || st[tp].b <= pos[i].b)) tp--;
		pre[i] = st[tp].ind;
		st[++tp] = pos[i];
		// if (tp == 1) {
		// 	tot++;
		// }
		// printf("%d ", tot);
	}

	// for (int i = 1; i <= n; i++) {
	// 	printf("%d ", pre[i]);
	// }

	for (int i = 1; i <= n; i++) pret.update(pret.root[i] = pret.root[i - 1], 0, n, pre[i], 1);
	for (int i = 1, l, r; i <= m; i++) {
		// scanf("%d %d", &l, &r);
		read(l), read(r);
		int tmp = pret.query(pret.root[r], 0, n, 0, l - 1) - pret.query(pret.root[l - 1], 0, n, 0, l - 1);
		// printf("%d\n", tmp);
		write(tmp), putchar('\n');
	}

	return 0;
}
```

</details>

## 讨论
### Link
[link](https://www.luogu.com.cn/problem/P8252)
### Sol
写了个神奇暴力在洛谷过了。。。   
大约的确出题人应该不会想到我这个做法，但愿卡不掉吧。。。   

---

思路很简单，对于每个人和他会做的题目连边，依次遍历每个人和他会做的每一道题，找到另外会做这道题的人，把他的贡献值加一， 在一个人遍历完之后查看有贡献的人，如果他的贡献值比双方会做的题数都要少，那么两个人就可以讨论。   
如果，这个人找不到讨论对象，就可以把连向他的所有边删掉。   
找到就直接输出。   

其实可以把这个做法卡到 $\mathcal{O(n^2)}$ 。   
