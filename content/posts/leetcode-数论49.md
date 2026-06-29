---
title: "leetcode-数论49"
date: 2026-06-20T11:09:57+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 数组的最大因子得分

给你一个整数数组 nums。

因子得分 定义为数组所有元素的最小公倍数（LCM）与最大公约数（GCD）的 乘积。

在 最多 移除一个元素的情况下，返回 nums 的 最大因子得分。

注意，单个数字的 LCM 和 GCD 都是其本身，而 空数组 的因子得分为 0。


```
impl Solution {
    pub fn max_score(nums: Vec<i32>) -> i64 {
        let n = nums.len();

        // 处理空数组和单元素数组的特殊情况
        if n == 0 {
            return 0;
        }
        if n == 1 {
            let val = nums[0] as i64;
            return val * val; // GCD 和 LCM 都是自身
        }

        // 后缀 GCD 和 LCM 数组
        // suf_gcd[i] = gcd(nums[i], nums[i+1], ..., nums[n-1])
        // suf_lcm[i] = lcm(nums[i], nums[i+1], ..., nums[n-1])
        let mut suf_gcd = vec![0; n + 1];
        let mut suf_lcm = vec![1_i64; n + 1];

        for i in (0..n).rev() {
            suf_gcd[i] = Self::gcd(suf_gcd[i + 1], nums[i] as i64);
            suf_lcm[i] = Self::lcm(suf_lcm[i + 1], nums[i] as i64);
        }

        // 不移除任何元素的情况
        let mut ans = suf_gcd[0] * suf_lcm[0];

        // 枚举移除每个元素
        let mut pre_gcd = 0_i64;
        let mut pre_lcm = 1_i64;

        for i in 0..n {
            // 移除 nums[i]，计算左侧 GCD/LCM 与右侧 GCD/LCM 的组合
            let curr_gcd = Self::gcd(pre_gcd, suf_gcd[i + 1]);
            let curr_lcm = Self::lcm(pre_lcm, suf_lcm[i + 1]);
            ans = ans.max(curr_gcd * curr_lcm);

            // 更新前缀 GCD 和 LCM（包含当前元素）
            pre_gcd = Self::gcd(pre_gcd, nums[i] as i64);
            pre_lcm = Self::lcm(pre_lcm, nums[i] as i64);
        }

        ans
    }

    /// 计算两个数的最大公约数（GCD）
    #[inline]
    fn gcd(a: i64, b: i64) -> i64 {
        if b == 0 {
            a.abs()
        } else {
            Self::gcd(b, a % b)
        }
    }

    /// 计算两个数的最小公倍数（LCM）
    /// 使用公式：lcm(a, b) = a / gcd(a, b) * b
    #[inline]
    fn lcm(a: i64, b: i64) -> i64 {
        if a == 0 || b == 0 {
            return a.max(b);
        }
        a / Self::gcd(a, b) * b
    }
}
```
