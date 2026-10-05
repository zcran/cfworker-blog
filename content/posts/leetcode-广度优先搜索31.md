---
title: "leetcode-广度优先搜索31"
date: 2026-09-13T09:32:08+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 跳跃游戏 IV

给你一个整数数组 arr ，你一开始在数组的第一个元素处（下标为 0）。

每一步，你可以从下标 i 跳到下标 i + 1 、i - 1 或者 j ：

i + 1 需满足：i + 1 < arr.length
i - 1 需满足：i - 1 >= 0
j 需满足：arr[i] == arr[j] 且 i != j

请你返回到达数组最后一个元素的下标处所需的 最少操作次数 。

注意：任何时候你都不能跳到数组外面。


```
use std::collections::{HashMap, VecDeque};

impl Solution {
    pub fn min_jumps(arr: Vec<i32>) -> i32 {
        let n = arr.len();
        if n == 1 {
            return 0;
        }

        // 记录每个值对应的所有下标，用于「同值跳跃」
        let mut value_to_indices: HashMap<i32, Vec<usize>> = HashMap::new();
        for (i, &v) in arr.iter().enumerate() {
            value_to_indices.entry(v).or_default().push(i);
        }

        // BFS：从下标 0 出发，求到下标 n-1 的最少步数
        let mut visited = vec![false; n];
        visited[0] = true;

        let mut queue = VecDeque::new();
        queue.push_back(0);

        let mut steps = 0;

        while !queue.is_empty() {
            // 逐层扩展，每层代表一步
            for _ in 0..queue.len() {
                let i = queue.pop_front().unwrap();

                if i == n - 1 {
                    return steps;
                }

                // 1. 相邻下标：i - 1 和 i + 1
                if i > 0 && !visited[i - 1] {
                    visited[i - 1] = true;
                    queue.push_back(i - 1);
                }
                if i + 1 < n && !visited[i + 1] {
                    visited[i + 1] = true;
                    queue.push_back(i + 1);
                }

                // 2. 同值跳跃：把所有与 arr[i] 相同的下标入队
                //    处理完后立即移除该值，避免后续重复遍历（关键优化）
                if let Some(indices) = value_to_indices.remove(&arr[i]) {
                    for j in indices {
                        if !visited[j] {
                            visited[j] = true;
                            queue.push_back(j);
                        }
                    }
                }
            }
            steps += 1;
        }

        -1
    }
}
```
