---
title: "leetcode-滑动窗口15"
date: 2026-07-18T11:02:30+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


##  三个无重叠子数组的最大和

给你一个整数数组 nums 和一个整数 k ，找出三个长度为 k 、互不重叠、且全部数字和最大的子数组，并返回这三个子数组。

以下标的数组形式返回结果，数组中的每一项分别指示每个子数组的起始位置（下标从 0 开始）。如果有多个结果，返回字典序最小的一个。




```
impl Solution {
    pub fn max_sum_of_three_subarrays(nums: Vec<i32>, k: i32) -> Vec<i32> {
        let k = k as usize;
        let n = nums.len();

        // 计算所有长度为 k 的子数组和
        let mut sums = vec![0; n - k + 1];
        let mut window_sum: i32 = nums.iter().take(k).sum();
        sums[0] = window_sum;
        for i in k..n {
            window_sum += nums[i] - nums[i - k];
            sums[i - k + 1] = window_sum;
        }

        // left[i]：在 [0..i] 范围内最大子数组和的起始索引
        let mut left = vec![0; sums.len()];
        let mut best_idx = 0;
        for i in 0..sums.len() {
            if sums[i] > sums[best_idx] {
                best_idx = i;
            }
            left[i] = best_idx;
        }

        // right[i]：在 [i..] 范围内最大子数组和的起始索引
        let mut right = vec![0; sums.len()];
        let mut best_idx = sums.len() - 1;
        for i in (0..sums.len()).rev() {
            if sums[i] >= sums[best_idx] {
                best_idx = i;
            }
            right[i] = best_idx;
        }

        // 枚举中间子数组
        let mut max_sum = 0;
        let mut result = vec![0, 0, 0];
        for mid in k..sums.len() - k {
            let left_idx = left[mid - k];
            let right_idx = right[mid + k];
            let total = sums[left_idx] + sums[mid] + sums[right_idx];
            if total > max_sum {
                max_sum = total;
                result = vec![left_idx as i32, mid as i32, right_idx as i32];
            }
        }

        result
    }
}
```
