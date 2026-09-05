---
title: "leetcode-前缀和3"
date: 2026-08-25T10:02:46+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 连续的子数组和

给你一个整数数组 nums 和一个整数 k ，如果 nums 有一个 好的子数组 返回 true ，否则返回 false：

一个 好的子数组 是：

长度 至少为 2 ，且
子数组元素总和为 k 的倍数。

注意：

子数组 是数组中 连续 的部分。
如果存在一个整数 n ，令整数 x 符合 x = n * k ，则称 x 是 k 的一个倍数。0 始终 视为 k 的一个倍数。


```
use std::collections::HashMap;

/// 检查是否存在长度至少为 2 且和为 k 的倍数的连续子数组
///
/// 核心思路：前缀和 + 哈希表
///
/// 1. 计算前缀和取模 k：prefix[i] = (nums[0] + ... + nums[i-1]) % k
/// 2. 如果 prefix[i] == prefix[j] (i < j)，说明子数组 nums[i..j-1] 的和是 k 的倍数
/// 3. 需要保证子数组长度至少为 2，即 j - i >= 2
/// 4. 哈希表记录每个余数第一次出现的位置
///
/// 时间复杂度 O(n)，空间复杂度 O(min(n, k))
impl Solution {
    pub fn check_subarray_sum(nums: Vec<i32>, k: i32) -> bool {
        // 特殊情况：k = 0 时需要特殊处理
        if k == 0 {
            // 需要找到长度 >= 2 且和为 0 的子数组
            // 即至少有两个相邻的 0
            for i in 1..nums.len() {
                if nums[i] == 0 && nums[i - 1] == 0 {
                    return true;
                }
            }
            return false;
        }

        // 哈希表：余数 -> 第一次出现的位置（前缀和索引）
        // 初始状态：前缀和为 0，位置为 0（表示没有元素）
        let mut seen = HashMap::with_capacity(nums.len());
        seen.insert(0, 0);

        let mut prefix_sum = 0;

        for (i, &num) in nums.iter().enumerate() {
            // 计算前缀和并取模
            prefix_sum = (prefix_sum + num) % k;
            // 处理负数取模（Rust 的 % 对负数会得到负数，需要修正）
            if prefix_sum < 0 {
                prefix_sum += k;
            }

            // 检查是否出现过相同的余数
            match seen.get(&prefix_sum) {
                Some(&prev_index) => {
                    // 当前长度 = i + 1 - prev_index
                    // 因为 prev_index 是前缀和索引，表示前 prev_index 个元素的和
                    if i + 1 - prev_index >= 2 {
                        return true;
                    }
                }
                None => {
                    // 第一次出现，记录位置
                    // 注意：位置是索引 + 1，表示前 i+1 个元素
                    seen.insert(prefix_sum, i + 1);
                }
            }
        }

        false
    }
}
```
