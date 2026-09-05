---
title: "leetcode-堆59"
date: 2026-08-21T09:54:14+08:00
tags: ["leetcode", "堆"]
draft: false
---


##  第 K 近障碍物查询

有一个无限大的二维平面。

给你一个正整数 k ，同时给你一个二维数组 queries ，包含一系列查询：

queries[i] = [x, y] ：在平面上坐标 (x, y) 处建一个障碍物，数据保证之前的查询 不会 在这个坐标处建立任何障碍物。

每次查询后，你需要找到离原点第 k 近 障碍物到原点的 距离 。

请你返回一个整数数组 results ，其中 results[i] 表示建立第 i 个障碍物以后，离原地第 k 近障碍物距离原点的距离。如果少于 k 个障碍物，results[i] == -1 。

注意，一开始 没有 任何障碍物。

坐标在 (x, y) 处的点距离原点的距离定义为 |x| + |y| 。


```
use std::collections::BinaryHeap;

impl Solution {
    pub fn results_array(queries: Vec<Vec<i32>>, k: i32) -> Vec<i32> {
        let k = k as usize;
        let mut ans = vec![-1; queries.len()];
        // 大根堆，维护 k 个最小的距离，堆顶即为第 k 近
        let mut heap = BinaryHeap::with_capacity(k + 1);

        for (i, q) in queries.iter().enumerate() {
            let dist = q[0].abs() + q[1].abs();
            heap.push(dist);
            if heap.len() > k {
                heap.pop(); // 弹出最大的，只保留 k 个最小
            }
            if heap.len() == k {
                ans[i] = *heap.peek().unwrap();
            }
        }

        ans
    }
}
```
