---
title: "leetcode-模拟115"
date: 2026-08-08T11:31:15+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## K 次乘运算后的最终数组 II

给你一个整数数组 nums ，一个整数 k  和一个整数 multiplier 。

你需要对 nums 执行 k 次操作，每次操作中：

找到 nums 中的 最小 值 x ，如果存在多个最小值，选择最 前面 的一个。

将 x 替换为 x * multiplier 。

k 次操作以后，你需要将 nums 中每一个数值对 109 + 7 取余。

请你返回执行完 k 次乘运算以及取余运算之后，最终的 nums 数组。


```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

impl Solution {
    const MOD: i64 = 1_000_000_007;

    /// 快速幂：计算 (base^exp) % MOD
    fn mod_pow(mut base: i64, mut exp: i64) -> i64 {
        let mut res = 1;
        base %= Self::MOD;
        while exp > 0 {
            if exp & 1 == 1 {
                res = res * base % Self::MOD;
            }
            base = base * base % Self::MOD;
            exp >>= 1;
        }
        res
    }

    /// 对 nums 执行 k 次操作后，每个元素对 MOD 取余
    ///
    /// 每次操作：找到最小值（最前面的），替换为 x * multiplier
    ///
    /// # 优化思路
    /// - multiplier == 1 时，所有值不变，直接返回
    /// - 先用小根堆将每个元素提升到 >= 当前最大值（消除大小差异）
    /// - 之后所有元素大小相近，剩余 k 次操作可均匀分配，用快速幂批量计算
    pub fn get_final_state(nums: Vec<i32>, k: i32, multiplier: i32) -> Vec<i32> {
        if multiplier == 1 {
            return nums;
        }

        let n = nums.len();
        let k = k as i64;
        let mult = multiplier as i64;
        let max_val = *nums.iter().max().unwrap() as i64;

        // 小根堆：(值, 原始索引)，Reverse 使 BinaryHeap 变成小根堆
        let mut heap: BinaryHeap<Reverse<(i64, usize)>> = nums
            .into_iter()
            .enumerate()
            .map(|(idx, val)| Reverse((val as i64, idx)))
            .collect();

        // 阶段一：用堆将最小值逐个提升，直到所有元素 >= max_val 或 k 用完
        let mut remaining = k;
        while remaining > 0 {
            let Reverse((val, _)) = heap.peek().unwrap();
            if *val >= max_val {
                break;
            }
            let Reverse((mut min_val, idx)) = heap.pop().unwrap();
            min_val *= mult;
            heap.push(Reverse((min_val, idx)));
            remaining -= 1;
        }

        // 阶段二：剩余操作均匀分配，快速幂批量计算
        // 将堆元素按 (值, 索引) 排序，确保分配顺序稳定
        let mut elements: Vec<Reverse<(i64, usize)>> = heap.into_vec();
        elements.sort_unstable_by_key(|Reverse((val, idx))| (*val, *idx));

        let mut result = vec![0; n];
        let full_rounds = remaining / n as i64;      // 每个元素额外乘的次数
        let extra = remaining % n as i64;            // 前 extra 个元素多乘一次

        for (i, Reverse((val, idx))) in elements.iter().enumerate() {
            let extra_times = if (i as i64) < extra { 1 } else { 0 };
            let total_mult = full_rounds + extra_times;
            result[*idx] = (val % Self::MOD * Self::mod_pow(mult, total_mult) % Self::MOD) as i32;
        }

        result
    }
}
```
