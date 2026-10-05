---
title: "leetcode-前缀和144"
date: 2026-08-25T10:02:55+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 按奇偶比统计子数组 I

给你一个整数数组 nums，以及两个整数 a 和 b。

对于一个 子数组 ，定义：

· x 表示其中偶数元素的数量。
· y 表示其中奇数元素的数量。

子数组中偶数与奇数的比例定义为 x / y，其中该比例按照精确的有理数值进行比较。

如果一个子数组满足以下条件，则称其为 有效子数组 ：

· y > 0，并且
· x / y <= a / b。

返回 nums 中有效子数组的数量。

子数组 是数组中一个连续的 非空 元素序列。


```
impl Solution {
    pub fn count_ratio_subarrays(nums: Vec<i32>, a: i32, b: i32) -> i32 {
        let n = nums.len();
        let a = a as i64;
        let b = b as i64;
        let mut ans = 0;

        for i in 0..n {
            let mut even = 0; // 偶数数量 x
            let mut odd = 0;  // 奇数数量 y
            for &num in &nums[i..] {
                if num & 1 == 1 {
                    odd += 1;
                } else {
                    even += 1;
                }
                // 检查条件: y > 0 且 x/y <= a/b  =>  x*b <= a*y
                if odd > 0 && even * b <= a * odd {
                    ans += 1;
                }
            }
        }

        ans
    }
}
```
