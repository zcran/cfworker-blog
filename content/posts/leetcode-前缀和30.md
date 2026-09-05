---
title: "leetcode-前缀和30"
date: 2026-08-25T10:02:47+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 所有奇数长度子数组的和

给你一个正整数数组 arr ，请你计算所有可能的奇数长度子数组的和。

子数组 定义为原数组中的一个连续子序列。

请你返回 arr 中 所有奇数长度子数组的和 。


```
impl Solution {
    /// 计算所有奇数长度子数组的和
    ///
    /// 对于每个元素 arr[i]，计算它出现在多少个奇数长度子数组中：
    /// - 左边选 l 个，右边选 r 个，使得 l + r + 1 为奇数
    /// - 即 l 和 r 必须同为奇数或同为偶数
    /// - 左边选择数：奇数个 l 有 (l+1)/2 种，偶数个 l 有 l/2 + 1 种
    /// - 右边同理，组合起来即得出现次数
    pub fn sum_odd_length_subarrays(arr: Vec<i32>) -> i32 {
        let n = arr.len();
        let mut ans = 0;

        for i in 0..n {
            let left = i;           // 左边可选元素个数
            let right = n - i - 1;   // 右边可选元素个数

            // 奇数个左边 × 奇数个右边 + 偶数个左边 × 偶数个右边
            let left_odd = (left + 1) / 2;
            let left_even = left / 2 + 1;
            let right_odd = (right + 1) / 2;
            let right_even = right / 2 + 1;

            let count = (left_odd * right_odd + left_even * right_even) as i32;
            ans += arr[i] * count;
        }

        ans
    }
}
```
