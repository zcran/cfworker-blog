---
title: "leetcode-前缀和6"
date: 2026-08-25T10:02:46+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 和为 K 的子数组

给你一个整数数组 nums 和一个整数 k ，请你统计并返回 该数组中和为 k 的子数组的个数 。

子数组是数组中元素的连续非空序列。


```
use std::collections::HashMap;

/// 统计和为 k 的子数组个数
///
/// 核心思路：前缀和 + 哈希表
///
/// 1. 计算前缀和 prefix[i] = sum(nums[0..i])
/// 2. 对于每个位置 i，需要找到之前出现的前缀和 prefix[j] 满足：
///    prefix[i] - prefix[j] = k → prefix[j] = prefix[i] - k
/// 3. 使用哈希表记录每个前缀和出现的次数
/// 4. 边遍历边统计，不需要存储完整的前缀和数组
///
/// 时间复杂度 O(n)，空间复杂度 O(n)
impl Solution {
    pub fn subarray_sum(nums: Vec<i32>, k: i32) -> i32 {
        // 哈希表：前缀和 -> 出现次数
        // 初始状态：前缀和为 0 出现 1 次（空子数组）
        let mut seen = HashMap::with_capacity(nums.len());
        seen.insert(0, 1);

        let mut prefix_sum = 0;
        let mut count = 0;

        for &num in nums.iter() {
            // 更新前缀和
            prefix_sum += num;

            // 查找是否存在前缀和 = prefix_sum - k
            // 如果存在，说明有 count 个子数组的和为 k
            if let Some(&freq) = seen.get(&(prefix_sum - k)) {
                count += freq;
            }

            // 记录当前前缀和
            *seen.entry(prefix_sum).or_insert(0) += 1;
        }

        count
    }
}
```
