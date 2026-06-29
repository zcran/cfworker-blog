---
title: "leetcode-数论11"
date: 2026-06-20T11:09:55+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 序列中不同最大公约数的数目

给你一个由正整数组成的数组 nums 。

数字序列的 最大公约数 定义为序列中所有整数的共有约数中的最大整数。

例如，序列 [4,6,16] 的最大公约数是 2 。

数组的一个 子序列 本质是一个序列，可以通过删除数组中的某些元素（或者不删除）得到。

例如，[2,5,10] 是 [1,2,1,2,4,1,5,10] 的一个子序列。

计算并返回 nums 的所有 非空 子序列中 不同 最大公约数的 数目 。


```
impl Solution {
    /// 计算所有非空子序列的不同 GCD 的数量
    ///
    /// 核心思路：
    /// 1. 枚举所有可能的 gcd 值 g（1..=max_num）
    /// 2. 检查是否存在子序列的 gcd 等于 g
    /// 3. 判断方法：取所有 g 的倍数的 gcd，如果等于 g，则存在
    ///
    /// 为什么这个判断有效：
    /// - 如果某个子序列的 gcd = g，那么该子序列中所有元素都是 g 的倍数
    /// - 取所有 g 的倍数的 gcd，得到的是包含所有 g 的倍数的子序列的 gcd
    /// - 如果这个 gcd 恰好等于 g，说明存在子序列的 gcd = g
    pub fn count_different_subsequence_gc_ds(nums: Vec<i32>) -> i32 {
        let max_num = *nums.iter().max().unwrap() as usize;

        // 标记数组中出现的数字
        let mut exists = vec![false; max_num + 1];
        for &num in &nums {
            exists[num as usize] = true;
        }

        let mut count = 0;

        // 枚举所有可能的 gcd 值
        for g in 1..=max_num {
            let mut cur_gcd = 0;

            // 遍历所有 g 的倍数
            let mut multiple = g;
            while multiple <= max_num {
                if exists[multiple] {
                    cur_gcd = Self::gcd(cur_gcd, multiple as i32);
                }
                multiple += g;
            }

            // 如果所有 g 的倍数的 gcd 等于 g，说明存在子序列的 gcd = g
            if cur_gcd == g as i32 {
                count += 1;
            }
        }

        count
    }

    /// 计算最大公约数（欧几里得算法）
    #[inline]
    fn gcd(mut a: i32, mut b: i32) -> i32 {
        while b != 0 {
            let temp = b;
            b = a % b;
            a = temp;
        }
        a
    }
}
```
