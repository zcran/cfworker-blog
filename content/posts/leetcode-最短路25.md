---
title: "leetcode-最短路25"
date: 2026-04-22T20:45:20+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## 使两个整数相等的数位操作

给你两个整数 n 和 m ，两个整数有 相同的 数位数目。

你可以执行以下操作 任意 次：

从 n 中选择 任意一个 不是 9 的数位，并将它 增加 1 。
从 n 中选择 任意一个 不是 0 的数位，并将它 减少 1 。

任意时刻，整数 n 都不能是一个 质数 ，意味着一开始以及每次操作以后 n 都不能是质数。

进行一系列操作的代价为 n 在变化过程中 所有 值之和。

请你返回将 n 变为 m 需要的 最小 代价，如果无法将 n 变为 m ，请你返回 -1 。

```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

const MAX_N: usize = 10000; // 题目最大数字范围

impl Solution {
    /// 计算将数字 n 通过每次改变一位（+1 或 -1）变为数字 m 的最小代价。
    /// 代价为路径上所有经过数字（包括起点和终点）的和。
    /// 只能移动到 **非质数** 的数字，且起点和终点本身也不能是质数。
    /// 若无法到达或起点/终点为质数，返回 -1。
    pub fn min_operations(n: i32, m: i32) -> i32 {
        let (n, m) = (n as usize, m as usize);
        // 预处理质数表 (true 表示质数)
        let is_prime = Self::prime_sieve(MAX_N);
        // 起点或终点为质数 → 无解
        if is_prime[n] || is_prime[m] {
            return -1;
        }

        // 确定数字的最大位数：limit 是比 max(n, m) 大的最小 10 的幂
        let max_val = n.max(m);
        let mut limit = 1;
        while limit <= max_val {
            limit *= 10;
        }
        let min_valid = limit / 10; // 最小有效数字（保证无前导零）

        // 距离数组，-1 表示未访问
        let mut dist = vec![-1_i64; limit];
        // 最小堆：(当前累计代价, 当前数字)
        let mut heap = BinaryHeap::new();
        heap.push(Reverse((n as i64, n)));

        // Dijkstra 主循环
        while let Some(Reverse((cost, x))) = heap.pop() {
            // 已确定最短距离，跳过过时状态
            if dist[x] != -1 {
                continue;
            }
            dist[x] = cost;

            // 到达终点，立即返回
            if x == m {
                return cost as i32;
            }

            // 尝试更改当前数字的每一位
            let mut base = 1;
            while base < limit {
                let digit = (x / base) % 10;
                // 当前位 +1 (不超过 9)
                if digit < 9 {
                    let y = x + base;
                    // 必须保持在 [min_valid, limit-1] 范围内，且为非质数，且未访问
                    if y >= min_valid
                        && y < limit
                        && !is_prime[y]
                        && dist[y] == -1
                    {
                        heap.push(Reverse((cost + y as i64, y)));
                    }
                }
                // 当前位 -1 (不小于 0)
                if digit > 0 {
                    let y = x - base;
                    if y >= min_valid
                        && !is_prime[y]
                        && dist[y] == -1
                    {
                        heap.push(Reverse((cost + y as i64, y)));
                    }
                }
                base *= 10;
            }
        }

        // 队列空且未到达 m
        -1
    }

    /// 埃拉托斯特尼筛法，返回 [0, max_n] 范围的质数布尔数组
    /// true 表示是质数，false 表示不是质数（合数或 0/1）
    fn prime_sieve(max_n: usize) -> Vec<bool> {
        let mut is_prime = vec![true; max_n + 1];
        is_prime[0] = false;
        is_prime[1] = false;
        for i in 2..=max_n {
            if is_prime[i] {
                let mut j = i * 2;
                while j <= max_n {
                    is_prime[j] = false;
                    j += i;
                }
            }
        }
        is_prime
    }
}
```
