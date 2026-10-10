---
title: "leetcode-动态规划87"
date: 2026-10-05T20:39:59+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 任意子数组和的绝对值的最大值

给你一个整数数组 nums 。一个子数组 [numsl, numsl+1, ..., numsr-1, numsr] 的 和的绝对值 为 abs(numsl + numsl+1 + ... + numsr-1 + numsr) 。

请你找出 nums 中 和的绝对值 最大的任意子数组（可能为空），并返回该 最大值 。

abs(x) 定义如下：

如果 x 是负整数，那么 abs(x) = -x 。
如果 x 是非负整数，那么 abs(x) = x 。


```
impl Solution {
    pub fn max_absolute_sum(nums: Vec<i32>) -> i32 {
        let mut ans = 0;      // 记录最大绝对值和
        let mut max_sum = 0;  // 以当前位置结尾的最大子数组和
        let mut min_sum = 0;  // 以当前位置结尾的最小子数组和

        for x in nums {
            // Kadane 思想：若之前累加反而变小，则从当前元素重新开始
            max_sum = max_sum.max(0) + x;
            min_sum = min_sum.min(0) + x;
            // 绝对值和最大 = max(最大正和, 最小负和的绝对值)
            ans = ans.max(max_sum).max(-min_sum);
        }

        ans
    }
}
```
