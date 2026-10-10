---
title: "leetcode-数学98"
date: 2026-10-07T20:39:35+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 数对的最大强度

给你一个整数数组 nums。

选择 恰好一对 不同下标 i 和 j。该数对的 强度 定义为：

(nums[i] * nums[j]) / gcd(nums[i], nums[j])2

返回所有可能数对中的 最大 强度。

gcd(a, b) 表示 a 和 b 的 最大公约数 。


```
impl Solution {
    pub fn max_pair_strength(nums: Vec<i32>) -> i64 {
        // 从大到小排序，便于利用最优性剪枝
        let mut nums = nums;
        nums.sort_unstable_by(|a, b| b.cmp(a));

        // 计算整个数组的 GCD，用于剪枝下界
        let all_gcd: i64 = nums.iter().fold(0i64, |acc, &x| gcd(acc, x as i64));
        let all_gcd2 = all_gcd * all_gcd;

        let mut ans: i64 = 0;
        for i in 0..nums.len() {
            let a = nums[i] as i64;
            for j in 0..i {
                let b = nums[j] as i64;
                let mul = a * b;
                // 剪枝：即使 gcd 取到下界 all_gcd，mul / all_gcd2 也无法超过 ans
                if mul / all_gcd2 <= ans {
                    break;
                }
                let g = gcd(a, b);
                let strength = mul / (g * g);
                if strength > ans {
                    ans = strength;
                }
            }
        }
        ans
    }
}

// 欧几里得算法求最大公约数
fn gcd(mut a: i64, mut b: i64) -> i64 {
    while b != 0 {
        let t = a % b;
        a = b;
        b = t;
    }
    a
}
```
