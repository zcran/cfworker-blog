---
title: "leetcode-贪心147"
date: 2026-10-03T12:55:44+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 到达数组末尾的最大得分

给你一个长度为 n 的整数数组 nums 。

你的目标是从下标 0 出发，到达下标 n - 1 处。每次你只能移动到 更大 的下标处。

从下标 i 跳到下标 j 的得分为 (j - i) * nums[i] 。

请你返回你到达最后一个下标处能得到的 最大总得分 。


```
impl Solution {
    pub fn find_maximum_score(nums: Vec<i32>) -> i64 {
        // 贪心：每次跳跃的得分 = (j - i) * nums[i]。
        // 由于要走到最后，总得分等价于对每一段距离乘以起点值。
        // 为了让乘积最大，应始终以目前为止遇到的最大 nums 值作为"起点值"，
        // 于是答案为 nums[0..n-1] 中前缀最大值的累加和。
        let mut ans = 0i64;
        let mut mx = 0i32;
        for i in 0..nums.len() - 1 {
            mx = mx.max(nums[i]);
            ans += mx as i64;
        }
        ans
    }
}
```
