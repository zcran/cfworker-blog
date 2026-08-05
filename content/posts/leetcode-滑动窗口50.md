---
title: "leetcode-滑动窗口50"
date: 2026-07-18T11:02:32+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 半径为 k 的子数组平均值

给你一个下标从 0 开始的数组 nums ，数组中有 n 个整数，另给你一个整数 k 。

半径为 k 的子数组平均值 是指：nums 中一个以下标 i 为 中心 且 半径 为 k 的子数组中所有元素的平均值，即下标在 i - k 和 i + k 范围（含 i - k 和 i + k）内所有元素的平均值。如果在下标 i 前或后不足 k 个元素，那么 半径为 k 的子数组平均值 是 -1 。

构建并返回一个长度为 n 的数组 avgs ，其中 avgs[i] 是以下标 i 为中心的子数组的 半径为 k 的子数组平均值 。

x 个元素的 平均值 是 x 个元素相加之和除以 x ，此时使用截断式 整数除法 ，即需要去掉结果的小数部分。

例如，四个元素 2、3、1 和 5 的平均值是 (2 + 3 + 1 + 5) / 4 = 11 / 4 = 2.75，截断后得到 2 。


```
impl Solution {
    pub fn get_averages(nums: Vec<i32>, k: i32) -> Vec<i32> {
        let k = k as usize;
        let n = nums.len();
        let window_size = 2 * k + 1;

        // 如果窗口大小大于数组长度，所有位置都无效
        if window_size > n {
            return vec![-1; n];
        }

        let mut result = vec![-1; n];
        let mut sum: i64 = 0;

        // 初始化第一个窗口
        for i in 0..window_size {
            sum += nums[i] as i64;
        }
        result[k] = (sum / window_size as i64) as i32;

        // 滑动窗口，计算后续位置
        for i in (window_size)..n {
            sum += nums[i] as i64;           // 加入新元素
            sum -= nums[i - window_size] as i64; // 移除离开窗口的元素
            result[i - k] = (sum / window_size as i64) as i32;
        }

        result
    }
}
```
