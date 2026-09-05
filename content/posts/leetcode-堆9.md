---
title: "leetcode-堆9"
date: 2026-08-21T09:54:11+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 分割数组为连续子序列

给你一个按 非递减顺序 排列的整数数组 nums 。

请你判断是否能在将 nums 分割成 一个或多个子序列 的同时满足下述 两个 条件：

每个子序列都是一个 连续递增序列（即，每个整数 恰好 比前一个整数大 1 ）。

所有子序列的长度 至少 为 3 。

如果可以分割 nums 并满足上述条件，则返回 true ；否则，返回 false 。


```
use std::collections::HashMap;

impl Solution {
    /// 贪心：每个数优先接在已有子序列末尾，否则尝试开启新子序列（需保证后面有两个数能续上）
    pub fn is_possible(nums: Vec<i32>) -> bool {
        // count[x] = 数组中数字 x 还剩多少个未分配
        let mut count: HashMap<i32, i32> = HashMap::new();
        // end[x] = 以 x 结尾的有效子序列个数
        let mut end: HashMap<i32, i32> = HashMap::new();

        for &x in &nums {
            *count.entry(x).or_insert(0) += 1;
        }

        for &x in &nums {
            if count[&x] == 0 {
                continue; // 已经分配完了
            }

            // 优先尝试接在已有子序列末尾
            if *end.get(&(x - 1)).unwrap_or(&0) > 0 {
                *end.get_mut(&(x - 1)).unwrap() -= 1;
                *end.entry(x).or_insert(0) += 1;
            }
            // 否则尝试开启新子序列（需要 x+1 和 x+2 都存在）
            else if *count.get(&(x + 1)).unwrap_or(&0) > 0
                 && *count.get(&(x + 2)).unwrap_or(&0) > 0
            {
                *count.get_mut(&(x + 1)).unwrap() -= 1;
                *count.get_mut(&(x + 2)).unwrap() -= 1;
                *end.entry(x + 2).or_insert(0) += 1;
            }
            // 两种都不行，则无法分割
            else {
                return false;
            }

            *count.get_mut(&x).unwrap() -= 1;
        }

        true
    }
}
```
