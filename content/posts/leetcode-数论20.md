---
title: "leetcode-数论20"
date: 2026-06-20T11:09:55+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 使数组可以被整除的最少删除次数

给你两个正整数数组 nums 和 numsDivide 。你可以从 nums 中删除任意数目的元素。

请你返回使 nums 中 最小 元素可以整除 numsDivide 中所有元素的 最少 删除次数。如果无法得到这样的元素，返回 -1 。

如果 y % x == 0 ，那么我们说整数 x 整除 y 。


```
impl Solution {
    pub fn min_operations(nums: Vec<i32>, nums_divide: Vec<i32>) -> i32 {
        // 计算 nums_divide 中所有数的最大公约数（GCD）
        // 目标 x 必须能整除这个 GCD，才能整除 nums_divide 中的所有数
        let target_gcd = nums_divide.iter().fold(nums_divide[0], |acc, &x| gcd(acc, x));

        // 排序以便找到最小的满足条件的元素
        let mut nums = nums;
        nums.sort_unstable();

        // 找到第一个能整除 target_gcd 的元素
        // 其下标即为需要删除的元素个数（因为前面的元素都不满足条件）
        for (i, &num) in nums.iter().enumerate() {
            if target_gcd % num == 0 {
                return i as i32;
            }
        }

        -1
    }
}

/// 欧几里得算法计算最大公约数（迭代版本）
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
