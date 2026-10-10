---
title: "leetcode-数组17"
date: 2026-10-10T20:38:41+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 解压缩编码列表

给你一个以行程长度编码压缩的整数列表 nums 。

考虑每对相邻的两个元素 [freq, val] = [nums[2*i], nums[2*i+1]] （其中 i >= 0 ），每一对都表示解压后子列表中有 freq 个值为 val 的元素，你需要从左到右连接所有子列表以生成解压后的列表。

请你返回解压后的列表。


```
impl Solution {
    /// 按行程长度编码解压数组。
    ///
    /// `nums` 每两个元素为一组 `[freq, val]`，表示解压后连续
    /// `freq` 个 `val`。遍历所有组，将 `val` 重复 `freq` 次追加到结果。
    ///
    /// 时间复杂度 O(总解压长度)，空间复杂度 O(总解压长度)。
    pub fn decompress_rl_elist(nums: Vec<i32>) -> Vec<i32> {
        // 预分配：每组贡献 freq 个元素，总长度为各组 freq 之和
        let total: usize = nums.iter().step_by(2).map(|&f| f as usize).sum();
        let mut result = Vec::with_capacity(total);

        for pair in nums.chunks_exact(2) {
            let (freq, val) = (pair[0] as usize, pair[1]);
            result.extend(std::iter::repeat(val).take(freq));
        }

        result
    }
}
```
