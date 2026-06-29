---
title: "leetcode-线段树15"
date: 2026-06-19T09:37:25+08:00
tags: ["leetcode", "线段树"]
draft: false
---


## 使子字符串变交替的最少删除次数

给你一个长度为 n 的字符串 s，其中仅包含字符 'A' 和 'B'。

你还获得了一个长度为 q 的二维整数数组 queries，其中每个 queries[i] 是以下形式之一：

· [1, j]：反转 s 中下标为 j 的字符，即 'A' 变为 'B'（反之亦然）。此操作会修改 s 并影响后续查询。
· [2, l, r]：计算 使 子字符串 s[l..r] 变成 交替字符串 所需的 最小 字符删除数。此操作不会修改 s；s 的长度保持为 n。

如果 子字符串 中不存在两个 相邻 字符 相等 的情况，则该子字符串是 交替字符串。长度为 1 的子字符串始终是交替字符串。

返回一个整数数组 answer，其中 answer[i] 是第 i 个类型为 [2, l, r] 的查询的结果。

子字符串 是字符串中一段连续的 非空 字符序列。


```
/// 线段树节点
/// 维护区间左右端点字符和使区间变交替所需的最小删除次数
#[derive(Clone, Copy)]
struct Node {
    left: u8,      // 区间左端点字符 (0='A', 1='B')
    right: u8,     // 区间右端点字符
    del: i32,      // 使区间变交替的最小删除次数
}

impl Node {
    fn new(ch: u8) -> Self {
        Node {
            left: ch,
            right: ch,
            del: 0,
        }
    }
}

/// 线段树，支持单点翻转和区间查询
struct SegmentTree {
    n: usize,
    tree: Vec<Node>,
}

impl SegmentTree {
    /// 用字符串 s 构建线段树
    fn new(s: &str) -> Self {
        let n = s.len();
        let mut tree = vec![Node { left: 0, right: 0, del: 0 }; 4 * n + 4];
        let mut seg = SegmentTree { n, tree };
        seg.build(s.as_bytes(), 1, 0, n - 1);
        seg
    }

    /// 构建线段树
    fn build(&mut self, s: &[u8], node: usize, l: usize, r: usize) {
        if l == r {
            self.tree[node] = Node::new(s[l] - b'A');
            return;
        }
        let mid = (l + r) >> 1;
        let left = node << 1;
        let right = left | 1;
        self.build(s, left, l, mid);
        self.build(s, right, mid + 1, r);
        self.tree[node] = self.merge(self.tree[left], self.tree[right]);
    }

    /// 合并左右子节点
    #[inline]
    fn merge(&self, left: Node, right: Node) -> Node {
        Node {
            left: left.left,
            right: right.right,
            // 如果左区间右端点 == 右区间左端点，合并时需要删除一个
            del: left.del + right.del + (left.right == right.left) as i32,
        }
    }

    /// 翻转位置 i 的字符
    fn flip(&mut self, idx: usize) {
        self.flip_rec(1, 0, self.n - 1, idx);
    }

    fn flip_rec(&mut self, node: usize, l: usize, r: usize, idx: usize) {
        if l == r {
            // 翻转字符：0->1, 1->0
            self.tree[node].left ^= 1;
            self.tree[node].right ^= 1;
            return;
        }
        let mid = (l + r) >> 1;
        let left = node << 1;
        let right = left | 1;
        if idx <= mid {
            self.flip_rec(left, l, mid, idx);
        } else {
            self.flip_rec(right, mid + 1, r, idx);
        }
        self.tree[node] = self.merge(self.tree[left], self.tree[right]);
    }

    /// 查询区间 [ql, qr] 的最小删除次数
    fn query(&self, ql: usize, qr: usize) -> i32 {
        self.query_rec(1, 0, self.n - 1, ql, qr).del
    }

    fn query_rec(&self, node: usize, l: usize, r: usize, ql: usize, qr: usize) -> Node {
        if ql <= l && r <= qr {
            return self.tree[node];
        }
        let mid = (l + r) >> 1;
        let left = node << 1;
        let right = left | 1;
        if qr <= mid {
            return self.query_rec(left, l, mid, ql, qr);
        }
        if ql > mid {
            return self.query_rec(right, mid + 1, r, ql, qr);
        }
        let left_res = self.query_rec(left, l, mid, ql, qr);
        let right_res = self.query_rec(right, mid + 1, r, ql, qr);
        self.merge(left_res, right_res)
    }
}

impl Solution {
    /// 处理查询：
    /// 类型 1: 翻转位置 j 的字符
    /// 类型 2: 查询使子串 s[l..r] 变交替的最小删除数
    ///
    /// 核心思路：
    /// 交替字符串要求相邻字符不同。
    /// 最小删除数 = 区间长度 - 最长交替子序列长度
    /// 但通过线段树维护相邻相等字符对的数量，答案即为区间内相邻相等字符对的数量。
    ///
    /// 时间复杂度 O((n + q) log n)，空间复杂度 O(n)
    pub fn min_deletions(s: String, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let mut seg = SegmentTree::new(&s);
        let mut ans = Vec::with_capacity(queries.len());

        for query in &queries {
            if query[0] == 1 {
                // 翻转字符
                let idx = query[1] as usize;
                seg.flip(idx);
            } else {
                // 查询区间
                let l = query[1] as usize;
                let r = query[2] as usize;
                ans.push(seg.query(l, r));
            }
        }

        ans
    }
}
```
