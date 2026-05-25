---
title: "leetcode-树状树组8"
date: 2026-05-03T20:07:52+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 子数组中占绝大多数的元素

设计一个数据结构，有效地找到给定子数组的 多数元素 。

子数组的 多数元素 是在子数组中出现 threshold 次数或次数以上的元素。

实现 MajorityChecker 类:

·MajorityChecker(int[] arr) 会用给定的数组 arr 对 MajorityChecker 初始化。

·int query(int left, int right, int threshold) 返回子数组中的元素  arr[left...right] 至少出现 threshold 次数，如果不存在这样的元素则返回 -1。


```
use std::collections::VecDeque;

/// 多数检查器，支持区间内查找出现次数不少于阈值的元素
/// 算法：摩尔投票（线段树维护区间候选与优势票数）+ 计数快速查询（位置数组 + 二分查找）
pub struct MajorityChecker {
    voter: SegmentTree, // 线段树，用于快速找出区间可能的多数元素（摩尔投票候选）
    counter: CountQuick, // 计数助手，用于验证候选元素的真实出现次数
}

/// 线段树节点存储 (候选元素, 优势票数)
/// 符合摩尔投票的合并规则：相同候选则票数相加，不同候选则票数相减（保留高票候选）
struct SegmentTree {
    n: usize,
    candidate: Vec<i32>, // 候选元素
    advantage: Vec<i32>, // 优势票数（差）
}

impl SegmentTree {
    /// 构建线段树，输入数组（将转为 1‑based 内部索引）
    fn new(arr: &[i32]) -> Self {
        let n = arr.len();
        let size = n * 4; // 线段树所需空间
        let mut tree = SegmentTree {
            n,
            candidate: vec![0; size],
            advantage: vec![0; size],
        };
        tree.build(arr, 1, 0, n - 1);
        tree
    }

    /// 递归构建线段树
    fn build(&mut self, arr: &[i32], node: usize, l: usize, r: usize) {
        if l == r {
            self.candidate[node] = arr[l];
            self.advantage[node] = 1;
            return;
        }
        let mid = (l + r) / 2;
        let left_child = node * 2;
        let right_child = node * 2 + 1;
        self.build(arr, left_child, l, mid);
        self.build(arr, right_child, mid + 1, r);

        // 合并两个子区间
        let (lc, rc) = (self.candidate[left_child], self.candidate[right_child]);
        let (la, ra) = (self.advantage[left_child], self.advantage[right_child]);
        if lc == rc {
            self.candidate[node] = lc;
            self.advantage[node] = la + ra;
        } else if la >= ra {
            self.candidate[node] = lc;
            self.advantage[node] = la - ra;
        } else {
            self.candidate[node] = rc;
            self.advantage[node] = ra - la;
        }
    }

    /// 查询区间 [l, r] 的摩尔投票候选元素 (0‑based)
    fn query(&mut self, l: usize, r: usize) -> i32 {
        self.query_range(1, 0, self.n - 1, l, r).0
    }

    /// 递归查询，返回 (候选元素, 优势票数)
    fn query_range(&mut self, node: usize, nl: usize, nr: usize, ql: usize, qr: usize) -> (i32, i32) {
        // 完全包含，直接返回节点值
        if ql <= nl && nr <= qr {
            return (self.candidate[node], self.advantage[node]);
        }
        let mid = (nl + nr) / 2;
        if qr <= mid {
            self.query_range(node * 2, nl, mid, ql, qr)
        } else if ql > mid {
            self.query_range(node * 2 + 1, mid + 1, nr, ql, qr)
        } else {
            let left = self.query_range(node * 2, nl, mid, ql, qr);
            let right = self.query_range(node * 2 + 1, mid + 1, nr, ql, qr);
            // 合并左右查询结果
            if left.0 == right.0 {
                (left.0, left.1 + right.1)
            } else if left.1 >= right.1 {
                (left.0, left.1 - right.1)
            } else {
                (right.0, right.1 - left.1)
            }
        }
    }
}

/// 计数助手：为每个数值存储其所有出现的位置（有序），用于快速统计区间内某数的出现次数
struct CountQuick {
    positions: Vec<Vec<usize>>, // positions[value] = 所有出现位置（升序）
}

impl CountQuick {
    /// 构建位置索引，arr 中数值范围应非负（题目保证）
    fn new(arr: &[i32]) -> Self {
        let max_val = *arr.iter().max().unwrap_or(&0) as usize;
        let mut positions = vec![Vec::new(); max_val + 1];
        for (idx, &val) in arr.iter().enumerate() {
            positions[val as usize].push(idx);
        }
        CountQuick { positions }
    }

    /// 统计数值 num 在闭区间 [l, r] 内出现的次数 (0‑based)
    fn count_in_range(&self, num: i32, l: usize, r: usize) -> i32 {
        let idx = num as usize;
        if idx >= self.positions.len() {
            return 0;
        }
        let pos_list = &self.positions[idx];
        // 使用 partition_point 找到第一个 > r 的位置和第一个 >= l 的位置
        let right_bound = pos_list.partition_point(|&x| x <= r);
        let left_bound = pos_list.partition_point(|&x| x < l);
        (right_bound - left_bound) as i32
    }
}

impl MajorityChecker {
    pub fn new(arr: Vec<i32>) -> Self {
        let voter = SegmentTree::new(&arr);
        let counter = CountQuick::new(&arr);
        MajorityChecker { voter, counter }
    }

    /// 查询区间 [left, right] 内出现次数不少于 threshold 的元素，若不存在则返回 -1
    pub fn query(&mut self, left: i32, right: i32, threshold: i32) -> i32 {
        let l = left as usize;
        let r = right as usize;
        let candidate = self.voter.query(l, r);
        if self.counter.count_in_range(candidate, l, r) >= threshold {
            candidate
        } else {
            -1
        }
    }
}

// 以下为题目要求的测试接口（保留原注释风格）
/**
 * Your MajorityChecker object will be instantiated and called as such:
 * let obj = MajorityChecker::new(arr);
 * let ret_1: i32 = obj.query(left, right, threshold);
 */
```
