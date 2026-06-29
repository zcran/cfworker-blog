---
title: "leetcode-数论23"
date: 2026-06-20T11:09:55+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 最大公因数等于 K 的子数组数目

给你一个整数数组 nums 和一个整数 k ，请你统计并返回 nums 的子数组中元素的最大公因数等于 k 的子数组数目。

子数组 是数组中一个连续的非空序列。

数组的最大公因数 是能整除数组中所有元素的最大整数。


```
impl Solution {
    pub fn subarray_gcd(nums: Vec<i32>, k: i32) -> i32 {
        let n = nums.len();
        let mut ans = 0;

        // 枚举每个子数组的起点，扩展右端点
        for i in 0..n {
            let mut g = 0;
            for j in i..n {
                g = gcd(g, nums[j]);
                if g == k {
                    ans += 1;
                } else if g < k {
                    // GCD 只会递减，如果已经小于 k，继续扩展只会更小
                    break;
                }
                // g > k 时继续扩展
            }
        }

        ans
    }
}

/// 计算两个数的最大公约数（迭代版本）
#[inline]
fn gcd(mut a: i32, mut b: i32) -> i32 {
    while b != 0 {
        let temp = b;
        b = a % b;
        a = temp;
    }
    a
}
```
