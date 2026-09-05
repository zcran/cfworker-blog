---
title: "leetcode-前缀和76"
date: 2026-08-25T10:02:50+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 统计中位数为 K 的子数组

给你一个长度为 n 的数组 nums ，该数组由从 1 到 n 的 不同 整数组成。另给你一个正整数 k 。

统计并返回 nums 中的 中位数 等于 k 的非空子数组的数目。

注意：

数组的中位数是按 递增 顺序排列后位于 中间 的那个元素，如果数组长度为偶数，则中位数是位于中间靠 左 的那个元素。
例如，[2,3,1,4] 的中位数是 2 ，[8,4,3,5,1] 的中位数是 4 。

子数组是数组中的一个连续部分。


```
impl Solution {
    pub fn count_subarrays(nums: Vec<i32>, k: i32) -> i32 {
        use std::collections::HashMap;

        let mut ans = 0;
        let mut balance = 0;           // 当前前缀的平衡值（大于k的贡献+1，小于k的贡献-1）
        let mut found_k = false;       // 是否已经遇到过k
        let mut prefix_counts = HashMap::new();
        prefix_counts.insert(0, 1);    // 空前缀的平衡值为0

        for &num in &nums {
            // 更新平衡值：大于k为+1，小于k为-1，等于k为0
            balance += if num > k { 1 } else if num < k { -1 } else { 0 };

            // 如果遇到k，标记已找到，并开始统计答案
            if num == k {
                found_k = true;
            }

            if found_k {
                // 当前前缀包含k，统计以当前位置结尾的子数组
                // 条件：前缀平衡差为0或1（中位数为k）
                ans += prefix_counts.get(&balance).copied().unwrap_or(0)
                     + prefix_counts.get(&(balance - 1)).copied().unwrap_or(0);
            } else {
                // 还未遇到k，只记录前缀平衡值
                *prefix_counts.entry(balance).or_insert(0) += 1;
            }
        }

        ans
    }
}
```
