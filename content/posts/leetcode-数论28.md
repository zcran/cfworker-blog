---
title: "leetcode-数论28"
date: 2026-06-20T11:09:56+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 范围内最接近的两个质数

给你两个正整数 left 和 right ，请你找到两个整数 num1 和 num2 ，它们满足：

· left <= nums1 < nums2 <= right  。
· nums1 和 nums2 都是 质数 。
· nums2 - nums1 是满足上述条件的质数对中的 最小值 。

请你返回正整数数组 ans = [nums1, nums2] 。如果有多个整数对满足上述条件，请你返回 nums1 最小的质数对。如果不存在符合题意的质数对，请你返回 [-1, -1] 。


```
const MX: usize = 1_000_000;

/// 使用埃氏筛预计算所有质数，并添加哨兵以避免边界检查
fn sieve() -> Vec<usize> {
    let mut is_prime = vec![true; MX + 1];
    is_prime[0] = false;
    is_prime[1] = false;

    // 埃氏筛核心
    let mut i = 2;
    while i * i <= MX {
        if is_prime[i] {
            let mut j = i * i;
            while j <= MX {
                is_prime[j] = false;
                j += i;
            }
        }
        i += 1;
    }

    // 收集所有质数
    let mut primes: Vec<usize> = (2..=MX)
        .filter(|&x| is_prime[x])
        .collect();

    // 添加哨兵，避免越界检查
    primes.push(usize::MAX);
    primes.push(usize::MAX);
    primes
}

/// 使用懒静态初始化，只在第一次调用时计算一次
lazy_static::lazy_static! {
    static ref PRIMES: Vec<usize> = sieve();
}

impl Solution {
    pub fn closest_primes(left: i32, right: i32) -> Vec<i32> {
        let left = left as usize;
        let right = right as usize;

        // 二分查找第一个 >= left 的质数位置
        let start = PRIMES.partition_point(|&x| x < left);

        let mut p = -1;
        let mut q = -1;
        let mut min_gap = usize::MAX;

        // 遍历相邻质数对
        let mut i = start;
        while i + 1 < PRIMES.len() && PRIMES[i + 1] <= right {
            let gap = PRIMES[i + 1] - PRIMES[i];
            if gap < min_gap {
                min_gap = gap;
                p = PRIMES[i] as i32;
                q = PRIMES[i + 1] as i32;
                // 最小可能间隙为2（孪生质数），可提前终止
                if gap == 2 {
                    break;
                }
            }
            i += 1;
        }

        vec![p, q]
    }
}
```
