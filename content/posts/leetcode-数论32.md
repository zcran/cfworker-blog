---
title: "leetcode-数论32"
date: 2026-06-20T11:09:56+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 使子数组元素和相等

给你一个下标从 0 开始的整数数组 arr 和一个整数 k 。数组 arr 是一个循环数组。换句话说，数组中的最后一个元素的下一个元素是数组中的第一个元素，数组中第一个元素的前一个元素是数组中的最后一个元素。

你可以执行下述运算任意次：

选中 arr 中任意一个元素，并使其值加上 1 或减去 1 。
执行运算使每个长度为 k 的 子数组 的元素总和都相等，返回所需要的最少运算次数。

子数组 是数组的一个连续部分。


```
impl Solution {
    /// 使所有长度为 k 的循环子数组和相等的最少操作次数
    ///
    /// 核心思路：
    /// 1. 循环数组中，所有长度为 k 的子数组和相等，等价于 arr[i] == arr[i + gcd(n,k)]
    /// 2. 因此数组被分成 g = gcd(n,k) 个独立的等价类，每个类中所有元素必须相等
    /// 3. 对每个等价类，将所有元素变为中位数即可使操作次数最少
    pub fn make_sub_k_sum_equal(arr: Vec<i32>, k: i32) -> i64 {
        let n = arr.len();
        let k = k as usize;
        let g = gcd(n, k);

        let mut total_ops = 0;

        // 对每个等价类进行处理
        for start in 0..g {
            // 收集该等价类的所有元素：arr[start], arr[start+g], arr[start+2g], ...
            let mut group: Vec<i32> = (start..n).step_by(g)
                .map(|i| arr[i])
                .collect();

            // 选择中位数（使绝对偏差和最小）
            let len = group.len();
            let mid = *group.select_nth_unstable(len / 2).1;

            // 计算该组所有元素到中位数的距离之和
            total_ops += group.iter()
                .map(|&x| (x as i64 - mid as i64).abs())
                .sum::<i64>();
        }

        total_ops
    }
}

/// 最大公约数（使用二进制 GCD 算法，对非负整数优化）
#[inline]
fn gcd(mut a: usize, mut b: usize) -> usize {
    if a == 0 { return b; }
    if b == 0 { return a; }

    // 提取公共的 2 的幂因子
    let shift = (a | b).trailing_zeros();
    a >>= a.trailing_zeros();
    b >>= b.trailing_zeros();

    while a != b {
        if a > b {
            std::mem::swap(&mut a, &mut b);
        }
        b -= a;
        b >>= b.trailing_zeros();
    }

    a << shift
}
```
