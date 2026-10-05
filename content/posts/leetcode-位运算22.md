---
title: "leetcode-位运算22"
date: 2026-09-24T10:33:35+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 按位与为零的三元组

给你一个整数数组 nums ，返回其中 按位与三元组 的数目。

按位与三元组 是由下标 (i, j, k) 组成的三元组，并满足下述全部条件：

0 <= i < nums.length
0 <= j < nums.length
0 <= k < nums.length
nums[i] & nums[j] & nums[k] == 0 ，其中 & 表示按位与运算符。


```
impl Solution {
    /// 统计满足 `nums[i] & nums[j] & nums[k] == 0` 的三元组个数。
    ///
    /// 思路：设 `cnt[x]` 为等于 `x` 的元素个数，`f[x]` 为所有 `x` 的超集
    /// （即 `x & y == x` 的 `y`）的元素总数。若三元组按位与结果恰好为 0，
    /// 由容斥原理，答案为 `Σ (-1)^popcount(x) * f[x]^3`。
    pub fn count_triplets(nums: Vec<i32>) -> i32 {
        // 取最大值确定需要枚举的二进制位数
        let max = *nums.iter().max().unwrap();
        let bits = if max == 0 { 1 } else { 32 - max.leading_zeros() };
        let size = 1usize << bits;

        // 统计每个数值出现的次数
        let mut f = vec![0i64; size];
        for &x in &nums {
            f[x as usize] += 1;
        }

        // 对每一位做超集前缀和：f[x] = Σ cnt[y]，其中 y 包含 x 的所有位
        for i in 0..bits {
            let bit = 1usize << i;
            for mask in 0..size {
                if mask & bit == 0 {
                    f[mask] += f[mask | bit];
                }
            }
        }

        // 容斥：答案 = Σ (-1)^popcount(mask) * f[mask]^3
        let mut result = 0i64;
        for mask in 0..size {
            let term = f[mask] * f[mask] * f[mask];
            result += if mask.count_ones() & 1 == 0 { term } else { -term };
        }

        result as i32
    }
}
```
