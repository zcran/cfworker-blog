---
title: "leetcode-哈希表12"
date: 2026-10-09T20:07:55+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 随机翻转矩阵

给你一个 m x n 的二元矩阵 matrix ，且所有值被初始化为 0 。请你设计一个算法，随机选取一个满足 matrix[i][j] == 0 的下标 (i, j) ，并将它的值变为 1 。所有满足 matrix[i][j] == 0 的下标 (i, j) 被选取的概率应当均等。

尽量最少调用内置的随机函数，并且优化时间和空间复杂度。

实现 Solution 类：

Solution(int m, int n) 使用二元矩阵的大小 m 和 n 初始化该对象
int[] flip() 返回一个满足 matrix[i][j] == 0 的随机下标 [i, j] ，并将其对应格子中的值变为 1
void reset() 将矩阵中所有的值重置为 0


```
use rand::prelude::*;
use std::collections::HashMap;

struct Solution {
    // 剩余可选格子数；下标映射用于把 [0, total) 中"已用"的位置换成末尾的有效位置。
    total: usize,
    remaining: usize,
    cols: usize,
    idx: HashMap<usize, usize>,
    rng: ThreadRng,
}

impl Solution {
    fn new(m: i32, n: i32) -> Self {
        let total = m as usize * n as usize;
        Solution {
            total,
            remaining: total,
            cols: n as usize,
            idx: HashMap::new(),
            rng: thread_rng(),
        }
    }

    fn flip(&mut self) -> Vec<i32> {
        // 从 [0, remaining) 中随机取一个位置 v，通过映射得到真实的空闲格子 x。
        let v = self.rng.gen_range(0..self.remaining);
        let x = *self.idx.entry(v).or_insert(v);

        // 把末尾位置 remaining-1 映射到 v，保证后续随机不会重复选中已用格子。
        self.remaining -= 1;
        let y = *self.idx.entry(self.remaining).or_insert(self.remaining);
        self.idx.insert(v, y);

        vec![(x / self.cols) as i32, (x % self.cols) as i32]
    }

    fn reset(&mut self) {
        self.remaining = self.total;
        self.idx.clear();
    }
}
```
