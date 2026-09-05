---
title: "leetcode-前缀和40"
date: 2026-08-25T10:02:48+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 每个查询的最大异或值

给你一个 有序 数组 nums ，它由 n 个非负整数组成，同时给你一个整数 maximumBit 。你需要执行以下查询 n 次：

1. 找到一个非负整数 k < 2maximumBit ，使得 nums[0] XOR nums[1] XOR ... XOR nums[nums.length-1] XOR k 的结果 最大化 。k 是第 i 个查询的答案。

2. 从当前数组 nums 删除 最后 一个元素。

请你返回一个数组 answer ，其中 answer[i]是第 i 个查询的结果。


```
impl Solution {
    /// 对每个前缀异或值，找到使结果最大化的 k
    ///
    /// 给定 mask = 2^maximumBit - 1，要最大化 xor ^ k，其中 k < 2^maximumBit
    /// 最优策略：k = xor ^ mask（即 k 的每一位取 xor 的反码）
    /// 因为 k 受限于 maximumBit 位，取反即可得到最大值
    pub fn get_maximum_xor(nums: Vec<i32>, maximum_bit: i32) -> Vec<i32> {
        let mask = (1 << maximum_bit) - 1;
        let mut xor = 0;
        let mut ans = Vec::with_capacity(nums.len());

        // 计算每个前缀异或值的最优 k
        for &num in &nums {
            xor ^= num;
            ans.push(xor ^ mask);
        }

        // 反转结果，因为是从后往前删除
        ans.reverse();
        ans
    }
}
```
