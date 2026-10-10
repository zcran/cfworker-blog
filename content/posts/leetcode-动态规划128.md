---
title: "leetcode-动态规划128"
date: 2026-10-05T20:40:02+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 求出所有子序列的能量和

给你一个长度为 n 的整数数组 nums 和一个 正 整数 k 。

一个整数数组的 能量 定义为和 等于 k 的子序列的数目。

请你返回 nums 中所有子序列的 能量和 。

由于答案可能很大，请你将它对 10^9 + 7 取余 后返回。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn sum_of_power(nums: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;
        // f[j]: 当前已处理元素中，和为 j 的子序列数目（作为「能量贡献」的累积）
        // 递推：对每个元素 x，所有旧子序列都可以选择不选（保留），
        // 也可以选择选入（若和不超 k），故 f[j] 先翻倍，再加上从 f[j-x] 转移来的部分
        let mut f = vec![0i64; k + 1];
        f[0] = 1;

        for &x in &nums {
            let x = x as usize;
            // 从大到小更新，保证每个元素只使用一次（0-1 背包）
            for j in (0..=k).rev() {
                f[j] = f[j] * 2 % MOD;
                if j >= x {
                    f[j] = (f[j] + f[j - x]) % MOD;
                }
            }
        }

        f[k] as i32
    }
}
```
