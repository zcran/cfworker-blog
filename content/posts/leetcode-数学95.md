---
title: "leetcode-数学95"
date: 2026-10-07T20:39:34+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 处理所有元素的成本

给你一个整数数组 nums 和一个整数 k。

初始时，你拥有 k 单位的资源。

你必须从左到右依次处理 nums 中的元素。处理第 i 个元素需要消耗 nums[i] 单位的资源。

如果当前可用资源少于 nums[i]，你可以执行一次操作，使可用资源增加 k。k 的值固定不变。第一次执行该操作的成本为 1，第二次的成本为 2，依此类推。

处理完第 i 个元素后，可用资源会减少 nums[i]。

返回处理完所有元素所需的 最小总成本。由于答案可能很大，请返回其对 10^9 + 7 取模后的结果。




```
impl Solution {
    pub fn minimum_cost(nums: Vec<i32>, k: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        const INV2: i64 = 500_000_004; // 2 在模 MOD 下的逆元

        // 所有元素总和
        let total: i64 = nums.iter().map(|&x| x as i64).sum();
        // 需要的操作次数 m = ceil(total / k) - 1 = (total - 1) / k
        let m = (total - 1) / k as i64;

        // 计算 m * (m + 1) / 2 模 MOD
        let m_mod = m % MOD;
        let res = m_mod * ((m_mod + 1) % MOD) % MOD * INV2 % MOD;

        res as i32
    }
}
```
