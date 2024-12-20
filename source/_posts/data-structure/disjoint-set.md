---
title: 并查集学习笔记
tags:
  - 并查集
  - 数据结构
categories: 数据结构
keywords:
  - 并查集
  - 数据结构
  - 信息学竞赛
description: 并查集学习笔记——十分优美的数据结构
hide: false
comments: true
mathjax: true
sticky: 0
sitemap: true
date: 2021-08-26 10:04:30
updated: 2021-10-19 09:50:00
---

## 并查集是什么

**并查集**为一种树型的数据结构。用于处理一些**不相交集合**（disjoint sets）的**合并**以及**查询**问题。

## 并查集能干什么

并查集主要使用来解决**分类问题**。

## 如何维护并查集

以[洛谷 P3367 【模板】并查集](https://www.luogu.com.cn/problem/P3367)为例。

题目所需要支持的操作有两种：

- 将 $x$ 与 $y$ 所在的**集合合并**。
- 查询 $x$ 与 $y$ 是否在**同一个集合**内。

{% note info 什么是集合？ %}

集合（set）指的是由一系列元素组成的整体，在这个整体内**没有顺序**，同时**同一个元素在同一个集合内只能出现一次**。

即集合具有无序性、确定性、互异性。

{% endnote %}

为了解决这一种类的问题，我们引入**并查集**。

### 初始化

一开始时每一个点都相当于一个集合，集合中的元素为他们自己。为了形象的表示并查集的原理，我们将**每一个集合**比喻为一个**帮派**（通过看[巨佬的博客](https://zhuanlan.zhihu.com/p/93647900)发现的好比喻），而帮派的**老大**（最具有代表性的元素），记为`root`，每一个点都拥有`root`，就像每一个人所属于的帮派都拥有老大一样。

$$
root: 1 \quad 2 \quad 3 \quad 4
$$

那么刚开始的这些帮派里**只有一个人**，并且这个人就是他们自己（自己当自己的老大）。

![初始化](https://cdn-bmyjacks-io.oss-accelerate.aliyuncs.com/img/data-structure/disjoint-set/1.png?x-oss-process=style/img)

```cpp
for (int i = 1; i <= N; ++i) {
    root[i] = i;
}
```

### 合并

当我们需要合并某两个帮派时，将这两个帮派的老大连接到一起，比如合并 $1$ 与 $2$：

![合并](https://cdn-bmyjacks-io.oss-accelerate.aliyuncs.com/img/data-structure/disjoint-set/2.png?x-oss-process=style/img)

这时由于同一个帮派**只能**有一个老大，所以从这两个人中随便选一个来当。

$$
root: 1 \quad 1 \quad 3 \quad 4
$$

```cpp
inline void merge(const int &x, const int &y) { root[x] = y; }
```

等等！直接把 $x$ 的老大设置为 $y$ 好像有点不对劲？可是又不知道为什么，那么看看这个例子：

一开始时的 $root$：

$$
root: 4 \quad 6 \quad 1 \quad 4 \quad 2 \quad 6 \quad
$$

![合并排错1](https://cdn-bmyjacks-io.oss-accelerate.aliyuncs.com/img/data-structure/disjoint-set/3.png?x-oss-process=style/img)

如果这时我们合并 $5$ 与 $3$ 时

$$
root: 4 \quad 3 \quad 1 \quad 4 \quad 2 \quad 6 \quad
$$

![合并排错2](https://cdn-bmyjacks-io.oss-accelerate.aliyuncs.com/img/data-structure/disjoint-set/4.png?x-oss-process=style/img)

就会发现原本与 $2$ 在同一个帮派（集合）内的 $6$ 现在竟然被独立出去了！

问题出现在哪里呢？当我们在合并之前并**不知道自己认为的老大是不是就是所在的帮派的老大**（帮派内可能还会有一些团体），所以我们需要一直询问下去，直到遇到某一个人说他自己就是他自己的帮派老大。

### 查询 root

```cpp
int findRoot(const int x) {
  if (x == root[x]) {
    return x;
  } else {
    return findRoot(root[x]);
  }
}
```

代码是不是十分简单呢？当我们查询到的人的老大并不是自己时继续查询老大的老大，虽然听起来有一些绕，但是通过这样我们就可以寻找到整个帮派里最大的“头目”，在合并的时候将他合并到另一个帮派里就没有问题了。

### 解释

经过了刚刚十分形象的比喻，我们明白了维护并查集需要使用的几个函数以及数组：

- `root`数组用来存储同一个集合内具有代表性的元素。
- `findRoot`函数用来寻找同一集合内**最具代表性**的元素。
- `merge`函数用来合并两个集合。

{% note info 经过上面的学习，贴出 code %}

```cpp
#include <iostream>

using namespace std;

constexpr int MAX_N = 1e4 + 10;

int root[MAX_N];

int findRoot(const int x) {
  if (x == root[x]) {
    return x;
  } else {
    return findRoot(root[x]);
  }
}

inline char check(const int &x, const int &y) {
  if (findRoot(x) == findRoot(y)) {
    return 'Y';
  } else {
    return 'N';
  }
}

inline void merge(const int &x, const int &y) { root[findRoot(x)] = y; }

int N = 0, M = 0;

int main() {
  ios::sync_with_stdio(false);

  cin >> N >> M;

  for (int i = 1; i <= N; ++i) {
    root[i] = i;
  }

  int Z, X, Y;
  for (int i = 1; i <= M; ++i) {
    cin >> Z >> X >> Y;

    if (Z == 1) {
      merge(X, Y);
    } else {
      cout << check(X, Y) << '\n';
    }
  }

  return 0;
}
```

{% endnote %}

提交之后惊讶地发现竟然只有 $20$ 分，是时候来讲讲优化了。

## 优化

### 路径压缩

当我们在查询最大的头目时，需要一个个向上查找，为什么不在查找之后直接将自己的老大设为最大的头目呢？这样，路径压缩优化已经完美理解。

![非路径压缩](https://cdn-bmyjacks-io.oss-accelerate.aliyuncs.com/img/data-structure/disjoint-set/5.png?x-oss-process=style/img)

当我们不使用路径压缩时，如果需要查询 $6$、$5$ 的最大头目时，需要向上查询 $9$ 次。

而我们使用路径压缩之后，查询 $6$ 的最大头目后整个集合变成了这样：

![路径压缩](https://cdn-bmyjacks-io.oss-accelerate.aliyuncs.com/img/data-structure/disjoint-set/6.png?x-oss-process=style/img)

这时查询 $6$ 和 $5$ 的查询次数就只有 $6$ 次了。

```cpp
int findRoot(const int x) {
  if (x == root[x]) {
    return x;
  } else {
    return root[x] = findRoot(root[x]);
  }
}
```

路径压缩后就 $100$ 了。

### 按秩合并

我们合并两个帮派的时候只是随意的将一个帮派的老大合并到另一个帮派里，那么可不可以将小帮派合并到大帮派里来提高速度呢？非常可以，这便是按秩合并。

#### 初始化

一开始时我们每一个人自成帮派时帮派的大小为 $1$。

$$
size_{x} = 1, x \in \left[1, n\right]
$$

```cpp
for (int i = 1; i <= n; ++i) {
    treeSize[i] = 1;
 }
```

#### 合并

合并时我们根据帮派的大小进行合并：

```cpp
void merge(const int x, const int y) {
  int xRoot = findRoot(x), yRoot = findRoot(y);

  if (treeSize[xRoot] > treeSize[yRoot]) {
    root[yRoot] = xRoot;
    treeSize[xRoot] += treeSize[yRoot];
  } else {
    root[xRoot] = yRoot;
    treeSize[yRoot] += treeSize[xRoot];
  }
}
```

{% note info 之前的一个小问题 %}

我们在合并两个帮派时采用了“合并其中一个的最大头目到另一个帮派里”的方法，如果我们将两个帮派的最大头目合并起来是不是就会更快了呢？还顺便进行了路径压缩。

{% endnote %}

## 扩展域并查集（种类并查集）

以[洛谷 P2024 [NOI2001] 食物链](https://www.luogu.com.cn/problem/P2024)为例，我们需要**维护两种关系**：“同类关系”与“敌人关系”。

而我们需要求出的是所有 $n$ 个动物所说的 $k$ 句话中有几句话是假的。

由于需要维护关系，我们自然想到使用并查集来维护这两种关系。但是普通的并查集无法同时维护两种关系，于是**扩展域并查集**诞生了。

$x$ 与 $y$ 的关系无非就三种：$x$ 与 $y$ 是同类， $x$ 被 $y$ 吃，$x$ 吃 $y$。

所以我们将`root`开到原来的 $3$ 倍。

$$
\begin{cases}
 root_{x}\text{ 与 }x\text{ 是同类}, & x \in \left[1, n\right] \\
 root_{x}\text{ 被 }x\text{ 吃}, & x \in \left[n + 1, 2n\right] \\
 root_{x}\text{ 吃 }x, & x \in \left[2n + 1, 3n\right]
\end{cases}
$$

那么对于每一个动物 $x$ 就有：

$$
\forall x \in \left[1, n\right]
\begin{cases}
 root_{x}\text{ 表示与 }x\text{ 是同类} \\
 root_{x + n}\text{ 是 }x\text{ 的猎物} \\
 root_{x + 2n}\text{ 吃 }x
\end{cases}
$$

则可以得到以下维护方案：

- 当所说的话为真话时，若 $x$ 与 $y$ 为同类，那么：

  $$
  merge(x, y) \\
  merge(x + n, y + n) \\
  merge(x + 2n, y + 2n)
  $$

- 若 $x$ 吃 $y$，那么：
  $$
  merge(x, y + 2n) \\
  merge(y, x + n) \\
  merge(x + 2n, y + n)
  $$

{% note info 为什么是这些维护方案？ %}

通过题意我们可以知道：

- **如果两个动物是同类的，那么他们的猎物，天敌同样是一类的。**
- **天敌的天敌为自己的猎物。**
- **猎物的猎物为自己的天敌。**

所以在合并的时候需要将自己的天敌与猎物同时进行处理。

{% endnote %}

{% note info 三个集合之间的关系到底是怎样的？ %}

$$
\forall x \in \left[1, n\right]
\begin{cases}
 root_{x}\text{ 为 }x\text{ 的同类} \\
 root_{x + n}\text{ 为 }x\text{ 的猎物} \\
 root_{x + 2n}\text{ 为 }x\text{ 的天敌} \\
 root_{x}\text{ 为 }x + n\text{ 的天敌} \\
 root_{x + n}\text{ 为 }x + n\text{ 的同类} \\
 root_{x + 2n}\text{ 为 }x + n\text{ 的猎物} \\
 root_{x}\text{ 为 }x + 2n\text{ 的猎物} \\
 root_{x + n}\text{ 为 }x + 2n\text{ 的天敌} \\
 root_{x + 2n}\text{ 为 }x + 2n\text{ 的同类} \\
\end{cases}
$$

{% endnote %}

{% note info code %}

```cpp
#include <iostream>

using namespace std;

const int MAX_N = 5e4 + 10;

int N = 0, K = 0;
int root[MAX_N * 3], treeSize[MAX_N * 3];

int findRoot(const int x) {
  if (x == root[x]) {
    return x;
  } else {
    return root[x] = findRoot(root[x]);
  }
}
inline void merge(const int x, const int y) {
  int rx = findRoot(x), ry = findRoot(y);

  if (treeSize[rx] < treeSize[ry]) {
    root[rx] = ry;
    treeSize[ry] += treeSize[rx];
  } else {
    root[ry] = rx;
    treeSize[rx] += treeSize[ry];
  }
}

inline bool check(const int x, const int y) {
  if (findRoot(x) == findRoot(y)) {
    return true;
  } else {
    return false;
  }
}

int main() {
  cin >> N >> K;

  for (int i = 0; i <= N * 3; ++i) {
    root[i] = i;
    treeSize[i] = 1;
  }

  int ans = 0;
  for (int i = 1, op, x, y; i <= K; ++i) {
    cin >> op >> x >> y;

    if ((x > N) || (y > N)) {
      ++ans;
      continue;
    }

    if (op == 1) {
      if (check(x, y + N) || check(x + N, y)) {
        ++ans;
      } else {
        merge(x, y);
        merge(x + N, y + N);
        merge(x + 2 * N, y + 2 * N);
      }
    } else {
      if (check(x, y) || check(x, y + N)) {
        ++ans;
      } else {
        merge(x + N, y);
        merge(x + 2 * N, y + N);
        merge(x, y + 2 * N);
      }
    }
  }

  cout << ans << endl;

  return 0;
}
```

{% endnote %}

## 带权并查集

以[洛谷 P1196 [NOI2002] 银河英雄传说](https://www.luogu.com.cn/problem/P1196)为例。

询问中包含对关系（在不在同一列）的指令，所以我们自然想到使用并查集进行维护。

但同时也会询问两艘战舰（同一列）之间间隔了多少战舰。为了记录这组数据，我们创建两个数组：

- `frontCnt[x]`表示 $x$ 的前面一共有 $frontCnt_{x}$ 艘战舰。
- `len[x]` 表示包含 $x$ 的战舰的那一列一共有 $len_{x}$ 艘战舰。

### 初始化

$$
root_{x} \gets x \newline
frontCnt_{x} \gets 0 \newline
len_{x} \gets 1
$$

### 路径压缩

```cpp
int findRoot(const int x) {
  if (root[x] == x) {
    return x;
  }

  int fx = findRoot(root[x]);
  frontCnt[x] += frontCnt[root[x]];
  return root[x] = fx;
}
```

### 合并

```cpp
void merge(const int &x, const int &y) {
  int fx = findRoot(x), fy = findRoot(y);

  frontCnt[fx] += len[fy];
  root[fx] = fy;
  len[fy] += len[fx];
  len[fx] = 0;
}
```

### 查询

```cpp
inline void query(const int &x, const int &y) {
  int fx = findRoot(x), fy = findRoot(y);

  if (fx != fy) {
    cout << -1;
  } else {
    cout << abs(frontCnt[x] - frontCnt[y]) - 1LL;
  }
  cout << '\n';
}
```

{% note info code %}

```cpp
#include <cmath>
#include <iostream>

using namespace std;

const int MAX_N = 3e4 + 10;

int t;
int root[MAX_N], frontCnt[MAX_N], len[MAX_N];

void init() {
  for (int i = 1; i < MAX_N; ++i) {
    root[i] = i;
    len[i] = 1;
  }
}

int findRoot(const int x) {
  if (root[x] == x) {
    return x;
  }

  int fx = findRoot(root[x]);
  frontCnt[x] += frontCnt[root[x]];
  return root[x] = fx;
}

void merge(const int &x, const int &y) {
  int fx = findRoot(x), fy = findRoot(y);

  frontCnt[fx] += len[fy];
  root[fx] = fy;
  len[fy] += len[fx];
  len[fx] = 0;
}

inline void query(const int &x, const int &y) {
  int fx = findRoot(x), fy = findRoot(y);

  if (fx != fy) {
    cout << -1;
  } else {
    cout << abs(frontCnt[x] - frontCnt[y]) - 1LL;
  }
  cout << '\n';
}

int main() {
  cin >> t;

  init();

  char op;
  for (int i = 1, x, y; i <= t; ++i) {
    cin >> op >> x >> y;
    if (op == 'M') {
      merge(x, y);
    } else {
      query(x, y);
    }
  }

  return 0;
}
```

{% endnote %}

## 亿些练习

- [洛谷 P3367 【模板】并查集](https://www.luogu.com.cn/problem/P3367)
- [洛谷 P1551 亲戚](https://www.luogu.com.cn/problem/P1551)
- [洛谷 P1396 营救](https://www.luogu.com.cn/problem/P1396)
