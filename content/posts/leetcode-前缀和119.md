---
title: "leetcode-前缀和119"
date: 2026-08-25T10:02:53+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 删除可整除和后的最小数组和

给你一个整数数组 nums 和一个整数 k。

你可以 多次 选择 连续 子数组 nums，其元素和可以被 k 整除，并将其删除；每次删除后，剩余元素会填补空缺。

返回在执行任意次数此类删除操作后，nums 的最小可能 和。


```
impl Solution {
    pub fn min_array_sum(nums: Vec<i32>, k: i32) -> i64 {
        let k = k as usize;
        // min_f[r] 表示前缀和模 k 为 r 时的最小前缀和
        let mut min_f = vec![i64::MAX; k];
        min_f[0] = 0;

        let mut f = 0i64;          // 当前最小前缀和
        let mut sum = 0usize;      // 当前前缀和模 k

        for &x in &nums {
            let x = x as i64;
            // 更新前缀和模 k
            sum = ((sum as i64 + x) % k as i64) as usize;

            // 状态转移：
            // 1. 不删除 x，保留它：f + x
            // 2. 删除以 x 结尾的连续子数组（和能被 k 整除）：min_f[sum]
            f = (f + x).min(min_f[sum]);

            // 维护当前前缀和模 k 的最小前缀和
            if f < min_f[sum] {
                min_f[sum] = f;
            }
        }

        f
    }
}
```
