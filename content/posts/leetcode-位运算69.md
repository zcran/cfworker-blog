---
title: "leetcode-位运算69"
date: 2026-09-24T10:33:38+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 构造最小位运算数组 I

给你一个长度为 n 的 质数 数组 nums 。你的任务是返回一个长度为 n 的数组 ans ，对于每个下标 i ，以下 条件 均成立：

ans[i] OR (ans[i] + 1) == nums[i]

除此以外，你需要 最小化 结果数组里每一个 ans[i] 。

如果没法找到符合 条件 的 ans[i] ，那么 ans[i] = -1 。

质数 指的是一个大于 1 的自然数，且它只有 1 和自己两个因数。


```
impl Solution {
    pub fn min_bitwise_array(nums: Vec<i32>) -> Vec<i32> {
        nums.into_iter()
            .map(|x| {
                // 条件：ans | (ans + 1) == x，最小化 ans。
                // 从低位起，ans 中连续的 1 会使其加 1 时逐位进位，
                // 我们尝试把最低的连续 1 段"截断"：令 d 从 1 开始，
                // 只要 x 当前位为 1，就尝试令 ans = x - d（把这一位及其低位的 1 去掉），
                // 直到遇到 x 的某一位为 0 为止。此时最后一个可行的 ans 即为最小值。
                let mut d = 1;
                let mut res = -1;
                while x & d != 0 {
                    res = x - d;
                    d <<= 1;
                }
                res
            })
            .collect()
    }
}
```
