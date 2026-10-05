---
title: "leetcode-二分查找70"
date: 2026-09-28T12:29:15+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 使数组成为等数数组的最小代价

给你一个整数数组 nums。

在一次 操作 中，你可以选择一个下标 i，并将 nums[i] 加 1 或减 1。

返回将 nums 中每个元素都变为 相同 的 正回文整数 所需的 最小 操作数。


```
use std::sync::OnceLock;

// 预处理所有回文数（从小到大）
fn palindromes() -> &'static Vec<i64> {
    static PAL: OnceLock<Vec<i64>> = OnceLock::new();
    PAL.get_or_init(|| {
        let mut pal = Vec::new();
        for base in [1_i64, 10, 100, 1000, 10000] {
            // 生成奇数长度回文数
            for i in base..base * 10 {
                let mut x = i;
                let mut t = i / 10;
                while t > 0 {
                    x = x * 10 + t % 10;
                    t /= 10;
                }
                pal.push(x);
            }
            // 生成偶数长度回文数
            if base <= 1000 {
                for i in base..base * 10 {
                    let mut x = i;
                    let mut t = i;
                    while t > 0 {
                        x = x * 10 + t % 10;
                        t /= 10;
                    }
                    pal.push(x);
                }
            }
        }
        pal.push(1_000_000_001); // 哨兵，防止下标越界
        pal
    })
}

impl Solution {
    pub fn minimum_cost(mut nums: Vec<i32>) -> i64 {
        let pal = palindromes();
        nums.sort_unstable();

        let n = nums.len();
        // 计算所有数变为 pal[i] 的总代价
        let cost = |i: usize| -> i64 {
            let target = pal[i];
            nums.iter().map(|&x| (x as i64 - target).abs()).sum()
        };

        // 找中位数右侧最近的回文数
        let median = nums[(n - 1) / 2] as i64;
        let i = pal.partition_point(|&p| p < median);
        if pal[i] <= nums[n / 2] as i64 {
            // 该回文数落在中位数区间内，直接使用
            cost(i)
        } else {
            // 否则枚举中位数两侧最近的两个回文数
            cost(i - 1).min(cost(i))
        }
    }
}
```
