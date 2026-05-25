---
title: "leetcode-记忆化11"
date: 2026-05-06T20:22:33+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 划分为k个相等的子集

给定一个整数数组  nums 和一个正整数 k，找出是否有可能把这个数组分成 k 个非空子集，其总和都相等。

```
impl Solution {
    /// 判断能否将数组 `nums` 分割成 `k` 个和相等的子集
    pub fn can_partition_k_subsets(nums: Vec<i32>, k: i32) -> bool {
        let k = k as usize;
        let total: i32 = nums.iter().sum();
        let max_num = *nums.iter().max().unwrap();

        // 基本条件检查：总和必须能被 k 整除，且最大元素不能超过子集和
        if total % k as i32 != 0 || max_num > total / k as i32 {
            return false;
        }

        let target = total / k as i32;          // 每个子集的目标和
        let mut buckets = vec![0; k];           // k 个桶，记录当前已放入元素的和
        let mut nums = nums;                    // 排序有助于剪枝（降序优先尝试大数）
        nums.sort_unstable_by(|a, b| b.cmp(a)); // 大数优先，减少分支

        Self::backtrack(&nums, 0, &mut buckets, target)
    }

    /// 深度优先回溯搜索
    /// - `nums`: 已排序的数组（降序）
    /// - `idx`: 当前尝试放入桶中的元素下标（从 0 开始，逐步增大）
    /// - `buckets`: 各桶的当前和
    /// - `target`: 每个桶的目标和
    fn backtrack(nums: &[i32], idx: usize, buckets: &mut Vec<i32>, target: i32) -> bool {
        // 所有元素都已成功放入桶中，说明找到了有效划分
        if idx == nums.len() {
            return true;
        }

        let current = nums[idx];

        // 遍历所有桶，尝试将 `current` 放入
        for i in 0..buckets.len() {
            // 剪枝1：放入后超过目标值，跳过此桶
            if buckets[i] + current > target {
                continue;
            }

            // 剪枝2：如果当前桶的和与前一个桶相同，跳过（避免重复搜索相同状态）
            // 因为元素降序排列，若前一个桶已经尝试过且失败，相同和的桶结果一致
            if i > 0 && buckets[i] == buckets[i - 1] {
                continue;
            }

            // 尝试放入当前桶
            buckets[i] += current;
            if Self::backtrack(nums, idx + 1, buckets, target) {
                return true; // 找到解，立即返回
            }
            // 撤销选择（回溯）
            buckets[i] -= current;
        }

        false // 所有桶都无法放置当前元素，此路径无解
    }
}
```
