---
title: "leetcode-位运算46"
date: 2026-09-24T10:33:37+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 找到两个数组的前缀公共数组

给你两个下标从 0 开始长度为 n 的整数排列 A 和 B 。

A 和 B 的 前缀公共数组 定义为数组 C ，其中 C[i] 是数组 A 和 B 到下标为 i 之前公共元素的数目。

请你返回 A 和 B 的 前缀公共数组 。

如果一个长度为 n 的数组包含 1 到 n 的元素恰好一次，我们称这个数组是一个长度为 n 的 排列 。


```
use std::collections::HashSet;

impl Solution {
    pub fn find_the_prefix_common_array(a: Vec<i32>, b: Vec<i32>) -> Vec<i32> {
        // 用一个集合记录已出现的公共元素（A、B 均为 1..n 的排列，不会重复）。
        let n = a.len();
        let mut seen = HashSet::with_capacity(n);
        let mut common = HashSet::with_capacity(n);
        let mut ans = Vec::with_capacity(n);

        for i in 0..n {
            // 先把 A[i]、B[i] 标记为已出现，再判断对方是否已出现。
            if !seen.insert(a[i]) {
                common.insert(a[i]);
            }
            if !seen.insert(b[i]) {
                common.insert(b[i]);
            }
            ans.push(common.len() as i32);
        }

        ans
    }
}
```
