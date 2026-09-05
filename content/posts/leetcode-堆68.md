---
title: "leetcode-堆68"
date: 2026-08-21T09:54:15+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 二进制交换后的最大分数

给你一个长度为 n 的整数数组 nums 和一个相同长度的二进制字符串 s。

一开始，你的分数为 0。对于每一个 s[i] = '1' 的下标 i，都会为分数贡献 nums[i]。

你可以执行 任意 次操作（包括零次）。在一次操作中，你可以选择一个下标 i（0 <= i < n - 1），满足 s[i] = '0' 且 s[i + 1] = '1'，并交换这两个字符。

返回一个整数，表示你可以获得的 最大可能分数。


```
use std::collections::BinaryHeap;

impl Solution {
    /// 核心思想：每个 '1' 位置需要选一个数来贡献分数。
    /// 交换规则允许 '1' 向左越过 '0'，意味着下标 i 处的 '1' 可以"吸收"其左侧任意一个
    /// 尚未被占用的数。贪心策略：每当遇到 '1'，从当前所有可用数中取最大值。
    pub fn maximum_score(nums: Vec<i32>, s: String) -> i64 {
        let mut heap = BinaryHeap::new();
        let mut ans: i64 = 0;

        // 同时遍历 nums 和 s，避免索引访问开销
        for (num, ch) in nums.into_iter().zip(s.chars()) {
            heap.push(num);
            if ch == '1' {
                // unwrap 安全：前面已 push，且 '1' 的数量不会超过已 push 的元素数
                ans += i64::from(heap.pop().unwrap());
            }
        }

        ans
    }
}
```
