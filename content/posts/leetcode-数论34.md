---
title: "leetcode-数论34"
date: 2026-06-20T11:09:56+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 使数组所有元素变成 1 的最少操作次数

给你一个下标从 0 开始的 正 整数数组 nums 。你可以对数组执行以下操作 任意 次：

选择一个满足 0 <= i < n - 1 的下标 i ，将 nums[i] 或者 nums[i+1] 两者之一替换成它们的最大公约数。
请你返回使数组 nums 中所有元素都等于 1 的 最少 操作次数。如果无法让数组全部变成 1 ，请你返回 -1 。

两个正整数的最大公约数指的是能整除这两个数的最大正整数。


```
impl Solution {
    /// 使数组所有元素都变为 1 的最少操作次数
    ///
    /// 核心思路：
    /// 1. 如果数组已有 1，只需将其他元素与 1 相邻操作即可
    /// 2. 否则需要先构造出一个 1，然后扩散到整个数组
    /// 3. 构造 1 的最短方法：找最短子数组使其 GCD 为 1
    pub fn min_operations(nums: Vec<i32>) -> i32 {
        let n = nums.len();

        // 统计 1 的个数和整体 GCD
        let (ones, total_gcd) = nums.iter().fold((0, 0), |(cnt, g), &x| {
            (cnt + if x == 1 { 1 } else { 0 }, gcd(g, x))
        });

        // 情况1：已有 1，每个非 1 元素需要一次操作
        if ones > 0 {
            return (n - ones) as i32;
        }

        // 情况2：整体 GCD > 1，无法构造出 1
        if total_gcd > 1 {
            return -1;
        }

        // 情况3：寻找最短子数组，使其 GCD 为 1
        let mut min_len = n + 1; // 初始化为不可能的大值
        for i in 0..n {
            let mut g = 0;
            for j in i..n {
                g = gcd(g, nums[j]);
                if g == 1 {
                    // 找到以 i 开头的最短有效子数组
                    let len = j - i + 1;
                    if len < min_len {
                        min_len = len;
                    }
                    break; // 更长的子数组不需要继续
                }
            }
        }

        // 需要 (min_len - 1) 次操作构造出 1，然后 (n - 1) 次操作扩散到整个数组
        (min_len - 1 + n - 1) as i32
    }
}

/// 计算两个数的最大公约数（欧几里得算法）
#[inline]
fn gcd(mut a: i32, mut b: i32) -> i32 {
    while b != 0 {
        let temp = a % b;
        a = b;
        b = temp;
    }
    a.abs()
}
```
