---
title: "leetcode-滑动窗口41"
date: 2026-07-18T11:02:31+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 得到连续 K 个 1 的最少相邻交换次数

给你一个整数数组 nums 和一个整数 k 。 nums 仅包含 0 和 1 。每一次移动，你可以选择 相邻 两个数字并将它们交换。

请你返回使 nums 中包含 k 个 连续 1 的 最少 交换次数。


```
impl Solution {
    pub fn min_moves(nums: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;
        // 收集所有 1 的位置（索引值）
        let ones: Vec<i64> = nums
            .iter()
            .enumerate()
            .filter_map(|(i, &v)| if v == 1 { Some(i as i64) } else { None })
            .collect();

        // 前缀和，方便 O(1) 获取区间和
        let mut prefix = vec![0; ones.len() + 1];
        for i in 0..ones.len() {
            prefix[i + 1] = prefix[i] + ones[i];
        }

        let mut min_swaps = i64::MAX;

        // 枚举所有包含 k 个 1 的连续窗口（在 ones 数组上）
        for left in 0..=ones.len() - k {
            let right = left + k - 1;
            let mid = (left + right) / 2;           // 中位数位置
            let median = ones[mid];

            // 左边元素个数（mid 左侧）
            let left_cnt = (mid - left) as i64;
            // 左边元素的原位置和
            let left_sum = prefix[mid] - prefix[left];
            // 左边元素的目标位置：从 median - left_cnt 到 median - 1
            let left_target = median * left_cnt - left_cnt * (left_cnt + 1) / 2;
            let left_cost = left_target - left_sum;

            // 右边元素个数（mid 右侧）
            let right_cnt = (right - mid) as i64;
            // 右边元素的原位置和
            let right_sum = prefix[right + 1] - prefix[mid + 1];
            // 右边元素的目标位置：从 median + 1 到 median + right_cnt
            let right_target = median * right_cnt + right_cnt * (right_cnt + 1) / 2;
            let right_cost = right_sum - right_target;

            min_swaps = min_swaps.min(left_cost + right_cost);
        }

        min_swaps as i32
    }
}
```
