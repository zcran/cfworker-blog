---
title: "Leetcode 水塘抽样3"
date: 2023-12-19T15:21:14+08:00
tags: ["leetcode", "水塘抽样"]
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
    n: usize,
    idx: HashMap<usize, usize>,
    rng: ThreadRng,
    r: usize,
    c: usize,
}

impl Solution {

    fn new(m: i32, n: i32) -> Self {
        Solution {
            n: m as usize * n as usize,
            idx: HashMap::new(),
            rng: thread_rng(),
            r: m as usize,
            c: n as usize,
        }
    }
    
    fn flip(&mut self) -> Vec<i32> {
        let v = self.rng.gen_range(0, self.n);
        let x = *self.idx.entry(v).or_insert(v);
        self.n -= 1;

        let y = *self.idx.entry(self.n).or_insert(self.n);
        *self.idx.entry(v).or_default() = y;

        vec![x as i32 / self.c as i32, x  as i32 % self.c  as i32]
    }
    
    fn reset(&mut self) {
        self.n = self.r * self.c;
        self.idx = HashMap::new();
    }
}
```