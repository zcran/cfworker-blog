---
title: "leetcode-枚举71"
date: 2026-07-09T10:05:02+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 识别数组中的最大异常值

给你一个整数数组 nums。该数组包含 n 个元素，其中 恰好 有 n - 2 个元素是 特殊数字 。剩下的 两个 元素中，一个是所有 特殊数字 的 和 ，另一个是 异常值 。

异常值 的定义是：既不是原始特殊数字之一，也不是表示元素和的那个数。

注意，特殊数字、和 以及 异常值 的下标必须 不同 ，但可以共享 相同 的值。

返回 nums 中可能的 最大异常值。


```
use std::collections::HashMap;

impl Solution {
    pub fn get_largest_outlier(mut nums: Vec<i32>) -> i32 {
        // 排序降序，便于从大到小尝试异常值
        nums.sort_unstable_by(|a, b| b.cmp(a));
        let total_sum: i32 = nums.iter().sum();

        // 统计每个数字出现次数
        let mut count = HashMap::new();
        for &x in &nums {
            *count.entry(x).or_insert(0) += 1;
        }

        // 枚举可能的异常值（从大到小）
        for &outlier in &nums {
            // 移除异常值后，剩余元素中需要找到一个特殊数字 special，
            // 使得 total_sum - outlier - special = special
            // 即 total_sum - outlier = 2 * special
            let remaining_sum = total_sum - outlier;

            // 如果剩余和是奇数，则不可能找到特殊数字
            if remaining_sum % 2 != 0 {
                continue;
            }

            let special = remaining_sum / 2;

            // 检查 special 是否在数组中，且不是 outlier 本身
            // 如果 special == outlier，需要至少出现 2 次（一次作为异常值，一次作为特殊数字）
            let need = if special == outlier { 2 } else { 1 };
            if let Some(&freq) = count.get(&special) {
                if freq >= need {
                    return outlier;
                }
            }
        }

        unreachable!()
    }
}
```
