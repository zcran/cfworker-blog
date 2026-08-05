---
title: "leetcode-滑动窗口53"
date: 2026-07-18T11:02:32+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 最少交换次数来组合所有的 1 II

交换 定义为选中一个数组中的两个 互不相同 的位置并交换二者的值。

环形 数组是一个数组，可以认为 第一个 元素和 最后一个 元素 相邻 。

给你一个 二进制环形 数组 nums ，返回在 任意位置 将数组中的所有 1 聚集在一起需要的最少交换次数。


```
impl Solution {
    pub fn min_swaps(nums: Vec<i32>) -> i32 {
        let total_ones = nums.iter().sum::<i32>() as usize;
        let n = nums.len();

        // 如果全为0或全为1，无需交换
        if total_ones <= 1 || total_ones == n {
            return 0;
        }

        // 双倍数组模拟环形
        let doubled: Vec<i32> = nums.iter().chain(nums.iter()).copied().collect();
        let window_size = total_ones;

        // 统计第一个窗口中1的个数
        let mut max_ones = doubled[0..window_size].iter().sum::<i32>();
        let mut current_ones = max_ones;

        // 滑动窗口遍历所有可能的聚集位置
        for i in window_size..n + window_size - 1 {
            current_ones += doubled[i] - doubled[i - window_size];
            max_ones = max_ones.max(current_ones);
        }

        // 最少交换次数 = 总1数 - 窗口内最多1数
        total_ones as i32 - max_ones
    }
}
```
