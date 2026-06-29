---
title: "leetcode-数论53"
date: 2026-06-20T11:09:57+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 最长乘积等价子数组

给你一个由 正整数 组成的数组 nums。

如果一个数组 arr 满足 prod(arr) == lcm(arr) * gcd(arr)，则称其为 乘积等价数组 ，其中：

prod(arr) 表示 arr 中所有元素的乘积。

gcd(arr) 表示 arr 中所有元素的最大公因数 (GCD)。

lcm(arr) 表示 arr 中所有元素的最小公倍数 (LCM)。

返回数组 nums 的 最长 乘积等价 子数组 的长度。


```
impl Solution {
    pub fn max_length(nums: Vec<i32>) -> i32 {
        let n = nums.len();

        // 从最长的子数组开始尝试
        for len in (1..=n).rev() {
            // 枚举所有长度为 len 的子数组
            for start in 0..=n - len {
                let end = start + len - 1;

                let mut prod = 1i64;      // 乘积可能溢出，使用 i64
                let mut gcd_val = nums[start];
                let mut lcm_val = nums[start];

                // 计算子数组的乘积、GCD 和 LCM
                for &val in &nums[start..=end] {
                    prod *= val as i64;
                    gcd_val = Self::gcd(gcd_val, val);
                    lcm_val = Self::lcm(lcm_val, val);
                }

                // 检查是否满足 prod == gcd * lcm
                if prod == (gcd_val as i64) * (lcm_val as i64) {
                    return len as i32;
                }
            }
        }

        0 // 理论上不会到达，因为长度为1的子数组总是满足条件
    }

    /// 计算两个数的最大公约数（GCD）
    #[inline]
    fn gcd(mut a: i32, mut b: i32) -> i32 {
        while b != 0 {
            let temp = a % b;
            a = b;
            b = temp;
        }
        a.abs()
    }

    /// 计算两个数的最小公倍数（LCM）
    #[inline]
    fn lcm(a: i32, b: i32) -> i32 {
        a / Self::gcd(a, b) * b
    }
}
```
