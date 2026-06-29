---
title: "leetcode-数论24"
date: 2026-06-20T11:09:55+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 最小公倍数等于 K 的子数组数目

给你一个整数数组 nums 和一个整数 k ，请你统计并返回 nums 的 子数组 中满足 元素最小公倍数为 k 的子数组数目。

子数组 是数组中一个连续非空的元素序列。

数组的最小公倍数 是可被所有数组元素整除的最小正整数。


```
impl Solution {
    /// 统计子数组的最小公倍数等于 k 的子数组数量
    ///
    /// # 示例
    /// ```
    /// use solution::Solution;
    /// assert_eq!(Solution::subarray_lcm(vec![3, 6, 2, 7, 1], 6), 4);
    /// assert_eq!(Solution::subarray_lcm(vec![3], 2), 0);
    /// ```
    pub fn subarray_lcm(nums: Vec<i32>, k: i32) -> i32 {
        let mut count = 0;

        for i in 0..nums.len() {
            let mut lcm = 1;

            for &num in &nums[i..] {
                // 剪枝：如果当前元素不能整除 k，后续 LCM 不可能等于 k
                if k % num != 0 {
                    break;
                }

                // 计算 LCM：a * b / gcd(a, b)
                lcm = lcm / gcd(lcm, num) * num;

                if lcm == k {
                    count += 1;
                }

                // 如果 LCM 已经超过 k，继续扩展只会更大
                if lcm > k {
                    break;
                }
            }
        }

        count
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
