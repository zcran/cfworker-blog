---
title: "leetcode-贪心47"
date: 2026-10-03T12:55:37+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 翻转子数组得到最大的数组值

给你一个整数数组 nums 。「数组值」定义为所有满足 0 <= i < nums.length-1 的 |nums[i]-nums[i+1]| 的和。

你可以选择给定数组的任意子数组，并将该子数组翻转。但你只能执行这个操作 一次 。

请你找到可行的最大 数组值 。


```
impl Solution {
    pub fn max_value_after_reverse(nums: Vec<i32>) -> i32 {
        let n = nums.len();

        // 原数组值：所有相邻元素差的绝对值之和
        let mut value = 0;
        for i in 0..n - 1 {
            value += (nums[i] - nums[i + 1]).abs();
        }

        // 情况一：翻转后边界变化带来的增益
        // 翻转子数组 [l, r]，只有两端相邻差被替换，收益为
        // |nums[l-1] - nums[r]| + |nums[l] - nums[r+1]| - 原两端差
        // 枚举端点，取最大增益
        let mut gain1 = 0;
        for i in 1..n - 1 {
            // 子数组从 i+1 开始（左端点为 nums[i]）
            gain1 = gain1
                .max((nums[0] - nums[i + 1]).abs() - (nums[i] - nums[i + 1]).abs());
            // 子数组到 i-1 结束（右端点为 nums[i]）
            gain1 = gain1
                .max((nums[n - 1] - nums[i - 1]).abs() - (nums[i] - nums[i - 1]).abs());
        }

        // 情况二：翻转后产生新的内部相邻差，增益为 2 * (区间最大值 - 区间最小值)
        // 遍历每条边，统计所有边的下界中的最大值、上界中的最小值
        let mut low_max = i32::MIN; // 各边 min(nums[i], nums[i+1]) 的最大值
        let mut high_min = i32::MAX; // 各边 max(nums[i], nums[i+1]) 的最小值
        for i in 0..n - 1 {
            low_max = low_max.max(nums[i].min(nums[i + 1]));
            high_min = high_min.min(nums[i].max(nums[i + 1]));
        }
        let gain2 = 2 * (low_max - high_min);

        value + gain1.max(gain2)
    }
}
```
