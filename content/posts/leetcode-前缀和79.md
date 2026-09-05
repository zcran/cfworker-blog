---
title: "leetcode-前缀和79"
date: 2026-08-25T10:02:51+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 左右元素和的差值

给你一个下标从 0 开始的长度为 n 的整数数组 nums。

定义两个数组 leftSum 和 rightSum，其中：

leftSum[i] 是数组 nums 中下标 i 左侧元素之和。如果不存在对应的元素，leftSum[i] = 0 。
rightSum[i] 是数组 nums 中下标 i 右侧元素之和。如果不存在对应的元素，rightSum[i] = 0 。

返回长度为 n 数组 answer，其中 answer[i] = |leftSum[i] - rightSum[i]|。


```
impl Solution {
    pub fn left_right_difference(nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();
        let mut ans = vec![0; n];

        // 第一次遍历：计算 leftSum，暂存到 ans
        let mut left_sum = 0;
        for i in 0..n {
            ans[i] = left_sum;
            left_sum += nums[i];
        }

        // 第二次遍历：计算 rightSum 并与 leftSum 求差
        let mut right_sum = 0;
        for i in (0..n).rev() {
            ans[i] = (ans[i] - right_sum).abs();
            right_sum += nums[i];
        }

        ans
    }
}
```
