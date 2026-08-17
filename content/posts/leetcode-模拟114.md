---
title: "leetcode-模拟114"
date: 2026-08-08T11:31:15+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## K 次乘运算后的最终数组 I

给你一个整数数组 nums ，一个整数 k  和一个整数 multiplier 。

你需要对 nums 执行 k 次操作，每次操作中：

找到 nums 中的 最小 值 x ，如果存在多个最小值，选择最 前面 的一个。

将 x 替换为 x * multiplier 。

请你返回执行完 k 次乘运算之后，最终的 nums 数组。


```
impl Solution {
    /// 对数组执行 k 次操作：每次将最小值（最前面的）乘以 multiplier
    ///
    /// # 参数
    /// - `nums`: 待操作的整数数组
    /// - `k`: 操作次数
    /// - `multiplier`: 乘数
    ///
    /// # 返回值
    /// - 执行 k 次操作后的数组
    pub fn get_final_state(mut nums: Vec<i32>, k: i32, multiplier: i32) -> Vec<i32> {
        for _ in 0..k {
            // 线性扫描找最小值索引，遇到更小值时更新（相等时保留前面的，自然满足）
            let min_idx = nums
                .iter()
                .enumerate()
                .min_by_key(|&(_, &val)| val)
                .map(|(idx, _)| idx)
                .unwrap();

            nums[min_idx] *= multiplier;
        }
        nums
    }
}
```
