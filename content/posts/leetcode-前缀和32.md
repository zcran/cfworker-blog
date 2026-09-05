---
title: "leetcode-前缀和32"
date: 2026-08-25T10:02:48+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 使数组和能被 P 整除

给你一个正整数数组 nums，请你移除 最短 子数组（可以为 空），使得剩余元素的 和 能被 p 整除。 不允许 将整个数组都移除。

请你返回你需要移除的最短子数组的长度，如果无法满足题目要求，返回 -1 。

子数组 定义为原数组中连续的一组元素。


```
use std::collections::HashMap;

impl Solution {
    /// 移除最短子数组，使剩余元素和能被 p 整除
    ///
    /// 思路：
    /// 1. 计算整个数组的和模 p，记为 k
    /// 2. 若 k == 0，无需移除
    /// 3. 否则需要移除一段子数组，其和模 p 等于 k
    /// 4. 使用前缀和 + 哈希表寻找最短的满足条件的子数组
    pub fn min_subarray(nums: Vec<i32>, p: i32) -> i32 {
        let total_rem = nums.iter().fold(0, |acc, &x| (acc + x) % p);

        // 整个数组和已能被 p 整除
        if total_rem == 0 {
            return 0;
        }

        let n = nums.len();
        let mut ans = n as i32;
        let mut prefix = 0;
        let mut last_pos = HashMap::new();
        last_pos.insert(0, -1); // 前缀和为 0 的位置在 -1

        for (i, &num) in nums.iter().enumerate() {
            prefix = (prefix + num) % p;

            // 需要找到前缀和 = (prefix - total_rem + p) % p 的最晚位置
            let target = (prefix - total_rem + p) % p;
            if let Some(&prev) = last_pos.get(&target) {
                ans = ans.min(i as i32 - prev);
            }

            // 记录当前前缀和的最晚位置（贪心：越靠右越有利于缩短子数组）
            last_pos.insert(prefix, i as i32);
        }

        // 如果 ans == n，说明只能移除整个数组，但题目不允许
        if ans == n as i32 { -1 } else { ans }
    }
}
```
