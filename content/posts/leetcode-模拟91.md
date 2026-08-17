---
title: "leetcode-模拟91"
date: 2026-08-08T11:31:14+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 矩阵中的和

给你一个下标从 0 开始的二维整数数组 nums 。一开始你的分数为 0 。你需要执行以下操作直到矩阵变为空：

1. 矩阵中每一行选取最大的一个数，并删除它。如果一行中有多个最大的数，选择任意一个并删除。
2. 在步骤 1 删除的所有数字中找到最大的一个数字，将它添加到你的 分数 中。

请你返回最后的 分数 。




```
impl Solution {
    /// LeetCode 2679: Sum in a Matrix
    ///
    /// 思路：每行降序排列后，逐列取所有行该位置的最大值累加。
    /// 原题每轮取每行最大值再取最大，等价于排序后按列取最大。
    /// 时间: O(n·m·log m)，额外空间: O(1)。
    pub fn matrix_sum(mut nums: Vec<Vec<i32>>) -> i32 {
        // 每行降序，每轮当前最大值就是 row[col]
        for row in nums.iter_mut() {
            row.sort_by(|a, b| b.cmp(a));
        }

        let cols = nums[0].len();
        let mut score = 0;

        // 逐列处理，每列取所有行该位置的最大值
        for col in 0..cols {
            score += nums.iter().map(|row| row[col]).max().unwrap();
        }

        score
    }
}
```
