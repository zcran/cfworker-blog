---
title: "leetcode-前缀和4"
date: 2026-08-25T10:02:46+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 连续数组

给定一个二进制数组 nums , 找到含有相同数量的 0 和 1 的最长连续子数组，并返回该子数组的长度。


```
use std::collections::HashMap;

/// 找到含有相同数量的 0 和 1 的最长连续子数组
///
/// 核心思路：前缀和 + 哈希表
///
/// 1. 将 0 视为 -1，1 视为 1
/// 2. 计算前缀和：prefix[i] = sum(nums[0..i])（其中 0 映射为 -1）
/// 3. 如果 prefix[i] == prefix[j] (i < j)，说明子数组 nums[i..j-1] 的和为 0
///    即 0 和 1 的数量相等
/// 4. 哈希表记录每个前缀和第一次出现的位置
/// 5. 最长子数组长度 = max(j - i)
///
/// 时间复杂度 O(n)，空间复杂度 O(n)
impl Solution {
    pub fn find_max_length(nums: Vec<i32>) -> i32 {
        // 哈希表：前缀和 -> 第一次出现的位置（索引）
        // 初始状态：前缀和为 0，位置为 -1（表示还没有元素）
        let mut seen = HashMap::with_capacity(nums.len() + 1);
        seen.insert(0, -1);

        let mut prefix_sum = 0;
        let mut max_len = 0;

        for (i, &num) in nums.iter().enumerate() {
            // 将 0 视为 -1，1 视为 1
            prefix_sum += if num == 0 { -1 } else { 1 };

            // 检查是否出现过相同的前缀和
            if let Some(&prev_index) = seen.get(&prefix_sum) {
                // 当前子数组长度 = i - prev_index
                let len = i as i32 - prev_index;
                if len > max_len {
                    max_len = len;
                }
            } else {
                // 第一次出现，记录位置
                seen.insert(prefix_sum, i as i32);
            }
        }

        max_len
    }
}
```
