---
title: "leetcode-数论57"
date: 2026-06-20T11:09:57+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 根据质数下标分割数组

给你一个整数数组 nums。

根据以下规则将 nums 分割成两个数组 A 和 B：

nums 中位于 质数 下标的元素必须放入数组 A。

所有其他元素必须放入数组 B。

返回两个数组和的 绝对 差值：|sum(A) - sum(B)|。

质数 是一个大于 1 的自然数，它只有两个因子，1和它本身。

注意：空数组的和为 0。


```
impl Solution {
    pub fn split_array(nums: Vec<i32>) -> i64 {
        // 使用 6k±1 优化质数判断
        fn is_prime(n: usize) -> bool {
            if n < 2 {
                return false;
            }
            if n == 2 || n == 3 {
                return true;
            }
            if n % 2 == 0 || n % 3 == 0 {
                return false;
            }

            let limit = (n as f64).sqrt() as usize;
            let mut i = 5;
            while i <= limit {
                if n % i == 0 || n % (i + 2) == 0 {
                    return false;
                }
                i += 6;
            }
            true
        }

        let mut sum_a: i64 = 0; // 质数下标放入 A
        let mut sum_b: i64 = 0; // 非质数下标放入 B

        for (i, &num) in nums.iter().enumerate() {
            if is_prime(i) {
                sum_a += num as i64;
            } else {
                sum_b += num as i64;
            }
        }

        (sum_a - sum_b).abs()
    }
}
```
