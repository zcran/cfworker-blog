---
title: "leetcode-位运算73"
date: 2026-09-24T10:33:38+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 不同 XOR 三元组的数目 I

给你一个长度为 n 的整数数组 nums，其中 nums 是范围 [1, n] 内所有数的 排列 。

XOR 三元组 定义为三个元素的异或值 nums[i] XOR nums[j] XOR nums[k]，其中 i <= j <= k。

返回所有可能三元组 (i, j, k) 中 不同 的 XOR 值的数量。

排列 是一个集合中所有元素的重新排列。


```
impl Solution {
    pub fn unique_xor_triplets(nums: Vec<i32>) -> i32 {
        // 结论：对于 n 个数的排列（元素为 [1, n]），所有三元组 (i<=j<=k) 的不同 XOR 值
        // 恰好覆盖 [0, 2^m - 1]，其中 2^m 是大于 n 的最小 2 的幂。
        //
        // 证明要点：
        // - 上界：nums 元素 < 2^m，任意异或值也 < 2^m，故 XOR 值种类 <= 2^m。
        // - 下界：可以构造出所有 2^m 个值（利用排列的完整性与 i<=j<=k 允许重复下标）。
        //
        // 特例：n <= 2 时结果退化，直接返回 n。

        let n = nums.len() as i32;
        if n <= 2 {
            return n;
        }

        // 找最小的 2 的幂使其 > n
        let mut ans = 1;
        while ans <= n {
            ans <<= 1;
        }
        ans
    }
}
```
