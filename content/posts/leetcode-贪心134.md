---
title: "leetcode-贪心134"
date: 2026-10-03T12:55:43+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 划分数组并满足最大差限制

给你一个长度为 n 的整数数组 nums，以及一个正整数 k 。

将这个数组划分为 n / 3 个长度为 3 的子数组，并满足以下条件：

子数组中 任意 两个元素的差必须 小于或等于 k 。

返回一个 二维数组 ，包含所有的子数组。如果不可能满足条件，就返回一个空数组。如果有多个答案，返回 任意一个 即可。


```
impl Solution {
    pub fn divide_array(mut nums: Vec<i32>, k: i32) -> Vec<Vec<i32>> {
        // 排序后每 3 个一组，组内最大差就是首尾差。
        // 若某组首尾差超过 k，则无解；否则该分组一定满足条件。
        nums.sort_unstable();

        let mut res = Vec::with_capacity(nums.len() / 3);
        for chunk in nums.chunks_exact(3) {
            if chunk[2] - chunk[0] > k {
                return vec![];
            }
            res.push(chunk.to_vec());
        }
        res
    }
}
```
