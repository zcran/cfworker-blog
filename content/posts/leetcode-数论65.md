---
title: "leetcode-数论65"
date: 2026-06-20T11:09:57+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 将数组转换为交替质数数组的最少操作次数

给你一个整数数组 nums。

如果满足以下条件，则认为数组是 交替质数 数组：

· 偶数 下标（从 0 开始）处的元素是 质数。

· 奇数 下标处的元素是 非质数。

在一次操作中，你可以将任何元素 增加 1。

返回将 nums 转换为 交替质数 数组所需的 最少 操作次数。

质数 是指大于 1 且仅有两个因数（1 和其本身）的自然数。


```
impl Solution {
    pub fn min_operations(nums: Vec<i32>) -> i32 {
        use std::sync::OnceLock;

        const MX: usize = 100_004;
        static IS_PRIME: OnceLock<[bool; MX]> = OnceLock::new();

        let is_prime = IS_PRIME.get_or_init(|| {
            let mut is_prime = [true; MX];
            is_prime[0] = false;
            is_prime[1] = false;

            let limit = (MX as f64).sqrt() as usize;
            for i in 2..=limit {
                if is_prime[i] {
                    for j in (i * i..MX).step_by(i) {
                        is_prime[j] = false;
                    }
                }
            }
            is_prime
        });

        let mut ans = 0;
        for (i, mut x) in nums.into_iter().enumerate() {
            let need_prime = i % 2 == 0;
            while is_prime[x as usize] != need_prime {
                ans += 1;
                x += 1;
            }
        }
        ans
    }
}
```
