---
title: "leetcode-有序集合19"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 由单个字符重复的最长子字符串

给你一个下标从 0 开始的字符串 s 。另给你一个下标从 0 开始、长度为 k 的字符串 queryCharacters ，一个下标从 0 开始、长度也是 k 的整数 下标 数组 queryIndices ，这两个都用来描述 k 个查询。

第 i 个查询会将 s 中位于下标 queryIndices[i] 的字符更新为 queryCharacters[i] 。

返回一个长度为 k 的数组 lengths ，其中 lengths[i] 是在执行第 i 个查询 之后 s 中仅由 单个字符重复 组成的 最长子字符串 的 长度 。


```
use std::cmp;

/// 线段树节点存储信息
#[derive(Clone, Copy, Default)]
struct Node {
    /// 前缀连续相同字符长度
    pref: i32,
    /// 后缀连续相同字符长度
    suff: i32,
    /// 最长连续相同字符长度
    max_len: i32,
}

/// 用于维护字符串中最长连续相同字符子串的线段树
struct SegmentTree {
    s: Vec<char>,
    tree: Vec<Node>,
}

impl SegmentTree {
    /// 创建新的线段树
    pub fn new(s: &str) -> Self {
        let chars: Vec<char> = s.chars().collect();
        let n = chars.len();
        let mut tree = vec![Node::default(); n * 4];
        let mut seg = SegmentTree { s: chars, tree };
        seg.build(1, 0, n - 1);
        seg
    }

    /// 更新指定位置的字符
    pub fn update(&mut self, pos: usize, ch: char) {
        self.s[pos] = ch;
        self.update_rec(1, 0, self.s.len() - 1, pos);
    }

    /// 获取当前最长连续相同字符长度
    pub fn get_max_len(&self) -> i32 {
        self.tree[1].max_len
    }

    /// 构建线段树
    fn build(&mut self, node: usize, left: usize, right: usize) {
        if left == right {
            self.tree[node] = Node {
                pref: 1,
                suff: 1,
                max_len: 1,
            };
            return;
        }

        let mid = (left + right) / 2;
        self.build(node * 2, left, mid);
        self.build(node * 2 + 1, mid + 1, right);
        self.pull(node, left, mid, right);
    }

    /// 更新节点（递归）
    fn update_rec(&mut self, node: usize, left: usize, right: usize, pos: usize) {
        if left == right {
            return;
        }

        let mid = (left + right) / 2;
        if pos <= mid {
            self.update_rec(node * 2, left, mid, pos);
        } else {
            self.update_rec(node * 2 + 1, mid + 1, right, pos);
        }
        self.pull(node, left, mid, right);
    }

    /// 合并子节点信息到父节点
    #[inline]
    fn pull(&mut self, node: usize, left: usize, mid: usize, right: usize) {
        let lc = node * 2;
        let rc = node * 2 + 1;
        let left_len = mid - left + 1;
        let right_len = right - mid;

        // 继承子节点的前缀和后缀
        let mut pref = self.tree[lc].pref;
        let mut suff = self.tree[rc].suff;
        let mut max_len = cmp::max(self.tree[lc].max_len, self.tree[rc].max_len);

        // 如果中间字符相同，可以合并
        if self.s[mid] == self.s[mid + 1] {
            // 如果左孩子全部相同，前缀可以扩展
            if self.tree[lc].pref == left_len as i32 {
                pref += self.tree[rc].pref;
            }
            // 如果右孩子全部相同，后缀可以扩展
            if self.tree[rc].suff == right_len as i32 {
                suff += self.tree[lc].suff;
            }
            // 中间合并形成的新连续段
            max_len = cmp::max(max_len, self.tree[lc].suff + self.tree[rc].pref);
        }

        self.tree[node] = Node { pref, suff, max_len };
    }
}

impl Solution {
    /// 处理字符更新查询，返回每次更新后的最长连续相同字符长度
    ///
    /// # 思路
    /// 使用线段树维护每个区间的前缀、后缀和最长连续长度
    /// 单点更新 O(log n)，查询 O(1)
    ///
    /// # 时间复杂度
    /// O((n + k) log n)
    ///
    /// # 空间复杂度
    /// O(n)
    pub fn longest_repeating(
        s: String,
        query_characters: String,
        query_indices: Vec<i32>,
    ) -> Vec<i32> {
        let query_chars: Vec<char> = query_characters.chars().collect();
        let mut seg_tree = SegmentTree::new(&s);
        let mut ans = Vec::with_capacity(query_indices.len());

        for (i, &idx) in query_indices.iter().enumerate() {
            let pos = idx as usize;
            let ch = query_chars[i];
            seg_tree.update(pos, ch);
            ans.push(seg_tree.get_max_len());
        }

        ans
    }
}
```
