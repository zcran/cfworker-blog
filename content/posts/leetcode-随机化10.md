---
title: "Leetcode 随机化10"
date: 2024-12-21T15:03:43+08:00
tags: ["leetcode", "随机化"]
draft: false
---

## 按权重随机选择

给你一个 下标从 0 开始 的正整数数组 w ，其中 w[i] 代表第 i 个下标的权重。

请你实现一个函数 pickIndex ，它可以 随机地 从范围 [0, w.length - 1] 内（含 0 和 w.length - 1）选出并返回一个下标。选取下标 i 的 概率 为 w[i] / sum(w) 。

例如，对于 w = [1, 3]，挑选下标 0 的概率为 1 / (1 + 3) = 0.25 （即，25%），而选取下标 1 的概率为 3 / (1 + 3) = 0.75（即，75%）。

```
use std::collections::BinaryHeap;
use std::cmp::Reverse;
use std::cmp::Ord;
use std::cmp::Ordering;

struct Probability {
    weight: i32,
    count: i32,
    idx: usize,
}

impl std::cmp::Ord for Probability {
    fn cmp(&self, other: &Probability) -> Ordering {
        return self.partial_cmp(other).unwrap();
    }
}

impl std::cmp::PartialOrd<Probability> for Probability {
    fn partial_cmp(&self, other: &Probability) -> Option<Ordering> {
        let this = self.count * other.weight;
        let that = other.count * self.weight;
        return Some(this.cmp(&that));
    }
}

impl std::cmp::Eq for Probability {
}

impl std::cmp::PartialEq<Probability> for Probability {
    fn eq(&self, that: &Probability) -> bool {
        return self.count * that.weight == that.count * self.weight;
    }
}

struct Solution {
    heap: BinaryHeap<Reverse<Probability>>,
}

impl Solution {

    fn new(w: Vec<i32>) -> Self {
        let mut heap = BinaryHeap::with_capacity(w.len());
        for (i, &x) in w.iter().enumerate() {
            heap.push(Reverse(Probability{ weight: x, count: 0, idx: i}));
        }
        Solution{ heap: heap }
    }
    
    fn pick_index(&mut self) -> i32 {
        let mut x = self.heap.pop().unwrap().0;
        x.count += 1;
        let ret = x.idx;
        self.heap.push(Reverse(x));
        return ret as i32;
    }
}
```