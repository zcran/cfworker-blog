---
title: "leetcode-位运算17"
date: 2026-09-24T10:33:35+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 错误的集合

集合 s 包含从 1 到 n 的整数。不幸的是，因为数据错误，导致集合里面某一个数字复制成了集合里面的另外一个数字的值，导致集合 丢失了一个数字 并且 有一个数字重复 。

给定一个数组 nums 代表了该集合发生错误后的结果。

请你找出重复出现的整数，再找到丢失的整数，将它们以数组的形式返回。




```
impl Solution {
    /// 核心思路（计数数组）：
    /// 用长度为 n+1 的计数数组统计 [1, n] 中每个数字出现的次数。
    /// - 出现 2 次的即为重复数字
    /// - 出现 0 次的即为丢失数字
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(n)。
    pub fn find_error_nums(nums: Vec<i32>) -> Vec<i32> {
        let mut cnt = vec![0u8; nums.len() + 1];

        for &n in &nums {
            cnt[n as usize] += 1;
        }

        let mut ans = vec![0; 2];
        for i in 1..=nums.len() {
            match cnt[i] {
                2 => ans[0] = i as i32,
                0 => ans[1] = i as i32,
                _ => {}
            }
        }

        ans
    }
}
```
