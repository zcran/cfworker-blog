---
title: "leetcode-数论58"
date: 2026-06-20T11:09:57+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 通过质数传送到达终点的最少跳跃次数

给你一个长度为 n 的整数数组 nums。

你从下标 0 开始，目标是到达下标 n - 1。

在任何下标 i 处，你可以执行以下操作之一：

移动到相邻格子：跳到下标 i + 1 或 i - 1，如果该下标在边界内。

质数传送：如果 nums[i] 是一个质数 p，你可以立即跳到任何满足 nums[j] % p == 0 的下标 j 处，且下标 j != i 。

返回到达下标 n - 1 所需的 最少 跳跃次数。

质数 是一个大于 1 的自然数，只有两个因子，1 和它本身。


```
use std::collections::HashMap;

/// 数值上限（根据题目约束调整）
const MX: usize = 1_000_001;

/// 预处理每个数的所有质因数（使用筛法）
lazy_static::lazy_static! {
    static ref FACTORS: Vec<Vec<i32>> = {
        let mut factors = vec![vec![]; MX];
        for i in 2..MX {
            // 如果 f[i] 为空，说明 i 是质数（筛法标记原理）
            if factors[i].is_empty() {
                // 将所有 i 的倍数加入质因数 i
                for j in (i..MX).step_by(i) {
                    factors[j].push(i as i32);
                }
            }
        }
        factors
    };
}

impl Solution {
    /// 计算从下标 0 到 n-1 的最少跳跃次数
    pub fn min_jumps(nums: Vec<i32>) -> i32 {
        let n = nums.len();

        // 构建质数 -> 索引列表的映射（仅当 nums[i] 本身是质数时）
        let mut prime_to_indices: HashMap<i32, Vec<usize>> = HashMap::new();
        for (idx, &value) in nums.iter().enumerate() {
            // 质数只有一个质因数（即它本身）
            if FACTORS[value as usize].len() == 1 {
                prime_to_indices.entry(value).or_default().push(idx);
            }
        }

        let mut steps = 0;
        let mut visited = vec![false; n];
        let mut queue = vec![n - 1]; // 从终点反向 BFS
        visited[n - 1] = true;

        // BFS 逐层扩展
        loop {
            let mut next_queue = vec![];

            for &idx in &queue {
                // 到达起点，返回步数
                if idx == 0 {
                    return steps;
                }

                // 1. 相邻跳跃：向左或向右移动一步
                if idx > 0 && !visited[idx - 1] {
                    visited[idx - 1] = true;
                    next_queue.push(idx - 1);
                }
                if idx + 1 < n && !visited[idx + 1] {
                    visited[idx + 1] = true;
                    next_queue.push(idx + 1);
                }

                // 2. 质数传送：通过 nums[idx] 的质因数跳跃
                for &prime in &FACTORS[nums[idx] as usize] {
                    if let Some(indices) = prime_to_indices.get_mut(&prime) {
                        for &target in indices.iter() {
                            if !visited[target] {
                                visited[target] = true;
                                next_queue.push(target);
                            }
                        }
                        // 清空已访问的索引，避免后续重复遍历
                        indices.clear();
                    }
                }
            }

            queue = next_queue;
            steps += 1;
        }
    }
}
```
