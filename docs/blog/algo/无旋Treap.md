---
title: 无旋Treap
createTime: 2026/07/25 17:48:34
permalink: /algo/6vx6her4/
---

## Links
[oi-wiki的讲解](https://oi-wiki.org/ds/treap/#%E6%97%A0%E6%97%8B-treap)
[loj的提交记录 普通](https://loj.ac/s/2606088)
[loj的提交记录 文艺](https://loj.ac/s/2606085)

## 模板说明
- `maxn` `INF` 数组大小和正无穷，可调
- `cnt` `root` 平衡树的节点数和根
- `Node` 节点
- `tr` 节点存储数组
- `newNode` 新建节点，返回tr下标
- `pushUp` `pushDown` 上传和下放，可调
- `split` `merge` 核心操作，分裂与合并，合并返回新根
- `askRank` `askRankLe` 已知元素查排名，分别为最小的`>=val`的和最大的`<=val`的
- `askKth` 查询第 $k$ 小的数的值
- `insert` `erase` 插入和删除
- `askPrev` `askNext` 查询前驱后继
- `modify` 修改操作，此处为翻转，可调
- `out` 输出序列

## 代码
```cpp
#include <bits/stdc++.h>
struct Treap {
  // changeable: maxn INF Node pushUp pushDown modify
  static constexpr int maxn = 100003;
  const long long INF = 0x3f3f3f3f3f3f3f3f;
  int cnt = 0, root = 0;
  struct Node {
    int l, r, size;
    long long val, prio, lazy;
    Node() : l(0), r(0), size(0), lazy(0), val(0), prio(0) {}
    Node(long long _val, long long _prio)
      : l(0), r(0), size(1), lazy(0), val(_val), prio(_prio) {}
  } tr[maxn];
  int newNode(long long val) {
    tr[++cnt] = Node(val, rand());
    return cnt;
  }
  void pushUp(int u) {
    tr[u].size = tr[tr[u].l].size + tr[tr[u].r].size + 1;
  }
  void pushDown(int u) {
    if(!u || !tr[u].lazy) return ;
    std::swap(tr[u].l, tr[u].r);
    tr[u].lazy = 0;
    if(tr[u].l) tr[tr[u].l].lazy ^= 1;
    if(tr[u].r) tr[tr[u].r].lazy ^= 1;
  }
  void split(int u, int k, int &a, int &b) {
    if(!u) {
      a = b = 0;
      return ;
    }
    pushDown(u);
    int sz = tr[tr[u].l].size;
    if(sz < k) {
      a = u;
      split(tr[u].r, k-sz-1, tr[u].r, b);
      pushUp(u);
    }
    else {
      b = u;
      split(tr[u].l, k, a, tr[u].l);
      pushUp(u);
    }
  }
  int merge(int a, int b) {
    if(1ll * a * b == 0) return a + b;
    if(tr[a].prio > tr[b].prio) {
      pushDown(a);
      tr[a].r = merge(tr[a].r, b);
      pushUp(a);
      return a;
    }
    else {
      pushDown(b);
      tr[b].l = merge(a, tr[b].l);
      pushUp(b);
      return b;
    }
  }
  int askRank(long long val) {
    int u = root, res = 0;
    while(u) {
      pushDown(u);
      if(val > tr[u].val) {
        res += tr[tr[u].l].size + 1;
        u = tr[u].r;
      }
      else {
        u = tr[u].l;
      }
    }
    return res + 1;
  }
  int askRankLe(long long val) {
    int u = root, res = 0;
    while(u) {
      pushDown(u);
      if(val >= tr[u].val) {
        res += tr[tr[u].l].size + 1;
        u = tr[u].r;
      }
      else {
        u = tr[u].l;
      }
    }
    return res;
  }
  long long askKth(int k) {
    if(k < 1 || k > tr[root].size) {
      return -INF;
    }
    int u = root;
    while(u) {
      pushDown(u);
      int sz = tr[tr[u].l].size;
      if(k <= sz) u = tr[u].l;
      else if(k == sz + 1) return tr[u].val;
      else k -= sz + 1, u = tr[u].r;
    }
    return -INF;
  }
  void insert(long long val) {
    int rank = askRank(val);
    int a, b;
    split(root, rank-1, a, b);
    root = merge(merge(a, newNode(val)), b);
  }
  void erase(long long val) {
    int rank = askRank(val);
    if(rank > tr[root].size || askKth(rank) != val) {
      return ;
    }
    int a, b, c;
    split(root, rank, a, c);
    split(a, rank-1, a, b);
    root = merge(a, c);
  }
  long long askPrev(long long val) {
    int rank = askRankLe(val-1);
    if(rank < 1) return -INF;
    return askKth(rank);
  }
  long long askNext(long long val) {
    int rank = askRank(val+1);
    if(rank > tr[root].size) return INF;
    return askKth(rank);
  }
  void modify(int l, int r) {
    int a, b, c;
    split(root, r, b, c);
    split(b, l-1, a, b);
    tr[b].lazy ^= 1;
    root = merge(a, merge(b, c));
  }
  void out(int u) {
    if(!u) return ;
    pushDown(u);
    out(tr[u].l);
    std::cout << tr[u].val << ' ';
    out(tr[u].r);
  }
};
int main() {
  srand(time(0));
  Treap tr;
  // TODO
}
```
