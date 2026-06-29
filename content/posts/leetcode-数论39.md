---
title: "leetcode-数论39"
date: 2026-06-20T11:09:56+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 完全子集的最大元素和


给你一个下标从 1 开始、由 n 个整数组成的数组。你需要从 nums 选择一个 完全集，其中每对元素下标的乘积都是一个 完全平方数，例如选择 ai 和 aj ，i * j 一定是完全平方数。

返回 完全子集 所能取到的 最大元素和 。

```
impl Solution {
    /// 返回完全子集的最大元素和
    ///
    /// 核心思路：将每个下标 i 映射到其"无平方因子部分"（square-free part）
    /// 如果两个下标的乘积是完全平方数，则它们映射到相同的值
    /// 因此将相同映射值的元素分组求和，取最大值
    ///
    /// 映射函数：将 i 中所有平方因子移除（如 12 = 2² * 3 → 3）
    pub fn maximum_sum(nums: Vec<i32>) -> i64 {
        let n = nums.len();
        // sum[core] 存储映射到 core 的所有元素之和
        let mut sums = vec![0_i64; n + 1];

        for (idx, &val) in nums.iter().enumerate() {
            let idx = idx + 1; // 转为 1-based 下标
            let core = square_free(idx);
            sums[core] += val as i64;
        }

        *sums.iter().max().unwrap_or(&0)
    }
}

/// 计算一个数的"无平方因子部分"（square-free part）
/// 移除所有平方因子，如 12 → 3, 18 → 2, 16 → 1
#[inline]
fn square_free(mut n: usize) -> usize {
    let mut d = 2;
    while d * d <= n {
        let square = d * d;
        while n % square == 0 {
            n /= square;
        }
        d += 1;
    }
    n
}
```
