---
title: "leetcode-动态规划181"
date: 2026-10-05T20:40:06+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 一个子数组循环移动后的最大脉冲值

给你一个长度为 n 的整数数组 nums。

定义整数数组 arr 的 脉冲值 为从下标 0 开始的 交替和 ：pulse(arr) = arr[0] - arr[1] + arr[2] - arr[3] + ...

你可以对 nums 执行 至多一次 操作：

选择两个下标 l 和 r，满足 0 <= l < r <= n - 1。
将子数组 nums[l..r] 循环左移恰好 一个位置。例如，[a, b, c, d] 变为 [b, c, d, a]。

返回执行 至多一次 该操作后可以获得的 最大脉冲值。

子数组 是数组中连续且 非空 的元素序列。


```
impl Solution {
    pub fn max_value(nums: Vec<i32>) -> i64 {
        // 原始交替和：nums[0] - nums[1] + nums[2] - ...
        let mut alter_sum: i64 = nums[0] as i64;
        for i in 1..nums.len() {
            let x = nums[i] as i64;
            alter_sum += if i % 2 == 1 { -x } else { x };
        }

        // 循环左移 [l..r] 等价于：把 nums[l] 移到 r 处，其余元素左移一位。
        // 交替和的变化量可视为一段"差分交替和"的最大子段和，乘以 2。
        // f0/f1 为滚动变量：处理到相邻差 d 时，f1 表示以当前位置结尾的最大交替差分和。
        let mut f0: i64 = 0;
        let mut f1: i64 = 0;
        let mut mx: i64 = 0;

        for i in 1..nums.len() {
            // d = nums[i] - nums[i-1]，符号由 i 的奇偶决定
            let d = nums[i] as i64 - nums[i - 1] as i64;
            let signed_d = if i % 2 == 1 { d } else { -d };
            let new_f = f0.max(0) + signed_d;
            f0 = f1;
            f1 = new_f;
            mx = mx.max(f1);
        }

        alter_sum + mx * 2
    }
}
```
