---
title: "leetcode-位运算6"
date: 2026-09-24T10:33:34+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 只出现一次的数字 III

给你一个整数数组 nums，其中恰好有两个元素只出现一次，其余所有元素均出现两次。 找出只出现一次的那两个元素。你可以按 任意顺序 返回答案。

你必须设计并实现线性时间复杂度的算法且仅使用常量额外空间来解决此问题。


```
impl Solution {
    /// 核心思路：
    /// 1. 全部元素异或，成对出现的元素抵消，结果 xor_all = a ^ b（a、b 为两个只出现一次的元素）。
    /// 2. xor_all 必然非 0，取其最低非 0 位（trailing_zeros）作为分组依据：
    ///    该位上 a 和 b 必然一个为 0、一个为 1，因此可将数组分成两组。
    /// 3. 每组内部各自异或，成对元素抵消，分别得到 a 和 b。
    ///
    /// 利用 `(x >> tz) & 1` 作为下标（0 或 1），把两组结果放进同一个 vec 里。
    pub fn single_number(nums: Vec<i32>) -> Vec<i32> {
        // xor_all = a ^ b，且一定不为 0
        let xor_all = nums.iter().fold(0, |acc, &x| acc ^ x);

        // a 与 b 在该位上必然不同，用它来分组
        let tz = xor_all.trailing_zeros();

        let mut ans = vec![0, 0];
        for x in nums {
            // 按第 tz 位分组：该位为 0 的进 ans[0]，为 1 的进 ans[1]
            ans[(x >> tz & 1) as usize] ^= x;
        }

        ans
    }
}
```
