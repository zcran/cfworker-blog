---
title: "leetcode-数论64"
date: 2026-06-20T11:09:57+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 数对的最大公约数之和

给你一个长度为 n 的整数数组 nums。

构造一个数组 prefixGcd，其中对于每个下标 i：

令 mxi = max(nums[0], nums[1], ..., nums[i])。
· prefixGcd[i] = gcd(nums[i], mxi)。
· 在构造 prefixGcd 之后：

将 prefixGcd 按 非递减 顺序排序。
· 通过取 最小的未配对 元素和 最大的未配对 元素来形成数对。
· 重复此过程，直到无法再形成更多数对。
· 对于每个形成的数对，计算 两个元素的最大公约数 gcd。
· 如果 n 是奇数，prefixGcd 数组中的 中间 元素保持 未配对 状态，并应被忽略。

返回一个整数，表示所有形成数对的 最大公约数之和。

术语 gcd(a, b) 表示 a 和 b 的 最大公约数。


```
impl Solution {
    /// 计算所有配对的最大公约数之和
    ///
    /// 1. 构造 prefixGcd: prefixGcd[i] = gcd(nums[i], max(nums[0..=i]))
    /// 2. 排序后首尾配对，计算每对 gcd 的总和
    pub fn gcd_sum(nums: Vec<i32>) -> i64 {
        let n = nums.len();
        if n == 0 {
            return 0;
        }

        // 构造 prefixGcd 数组
        let mut prefix_gcd = Vec::with_capacity(n);
        let mut max_so_far = 0;
        for &x in &nums {
            max_so_far = max_so_far.max(x);
            prefix_gcd.push(gcd(x, max_so_far));
        }

        // 排序
        prefix_gcd.sort_unstable();

        // 首尾配对计算 gcd 总和
        let mut ans = 0i64;
        for i in 0..n / 2 {
            ans += gcd(prefix_gcd[i], prefix_gcd[n - 1 - i]) as i64;
        }

        ans
    }
}

/// 计算两个数的最大公约数（欧几里得算法）
#[inline]
fn gcd(mut a: i32, mut b: i32) -> i32 {
    while b != 0 {
        let temp = b;
        b = a % b;
        a = temp;
    }
    a.abs()
}
```
