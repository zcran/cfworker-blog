---
title: "leetcode-滑动窗口24"
date: 2026-07-18T11:02:30+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 两个无重叠子数组的最大和

给你一个整数数组 nums 和两个整数 firstLen 和 secondLen，请你找出并返回两个无重叠 子数组 中元素的最大和，长度分别为 firstLen 和 secondLen 。

长度为 firstLen 的子数组可以出现在长为 secondLen 的子数组之前或之后，但二者必须是无重叠。

子数组是数组的一个 连续 部分。


```
impl Solution {
    pub fn max_sum_two_no_overlap(mut nums: Vec<i32>, first_len: i32, second_len: i32) -> i32 {
        let (f, s) = (first_len as usize, second_len as usize);
        // 前缀和，方便O(1)获取子数组和
        for i in 1..nums.len() {
            nums[i] += nums[i - 1];
        }
        // 初始值：两个子数组紧挨着放在最左边的情况
        let mut max_first = nums[f - 1];            // 第一个子数组在左
        let mut max_second = nums[s - 1];           // 第二个子数组在左
        let mut res = nums[f + s - 1];              // 两段紧挨着

        // 枚举右子数组的右边界 i（从 f+s 开始，保证左边有空间给另一个子数组）
        for i in f + s..nums.len() {
            // 情况1：first_len 在左，second_len 在右
            // 维护左侧 first_len 子数组的最大和
            let left_first = nums[i - s] - nums[i - s - f];
            max_first = max_first.max(left_first);
            // 当前 second_len 子数组和
            let cur_second = nums[i] - nums[i - s];
            res = res.max(max_first + cur_second);

            // 情况2：second_len 在左，first_len 在右
            // 维护左侧 second_len 子数组的最大和
            let left_second = nums[i - f] - nums[i - f - s];
            max_second = max_second.max(left_second);
            // 当前 first_len 子数组和
            let cur_first = nums[i] - nums[i - f];
            res = res.max(max_second + cur_first);
        }
        res
    }
}
```
