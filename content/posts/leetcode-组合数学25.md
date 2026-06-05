---
title: "leetcode-组合数学25"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 单面值组合的第 K 小金额


给你一个整数数组 coins 表示不同面额的硬币，另给你一个整数 k 。

你有无限量的每种面额的硬币。但是，你 不能 组合使用不同面额的硬币。

返回使用这些硬币能制造的 第 k^th 小 金额。

```
impl Solution {
    /// 返回使用每种硬币的倍数（不能混合面额）能制造的第 k 小的金额。
    /// 使用容斥原理计算不超过 m 的倍数个数，然后二分查找。
    pub fn find_kth_smallest(coins: Vec<i32>, k: i32) -> i64 {
        const MOD: i64 = 1_000_000_007; // 本题不需要，但保留以防其他使用
        let k = k as i64;
        // 去重并排序，减少枚举子集的数量
        let mut coins: Vec<i64> = coins.into_iter().map(|c| c as i64).collect();
        coins.sort();
        coins.dedup();
        let n = coins.len();
        if n == 0 {
            return 0; // 没有硬币，无法制造任何金额（根据题意不会出现）
        }
        let min_coin = coins[0];

        // 检查小于等于 m 的倍数个数是否 >= k
        let check = |m: i64| -> bool {
            let mut cnt = 0i64;
            // 枚举所有非空子集
            for mask in 1..(1 << n) {
                let mut lcm = 1i64;
                let mut bits = 0;
                // 计算子集的最小公倍数
                for j in 0..n {
                    if mask & (1 << j) != 0 {
                        bits += 1;
                        let a = lcm;
                        let b = coins[j];
                        // 计算 lcm(a, b) = a / gcd(a, b) * b，提前检查溢出
                        let g = gcd(a, b);
                        // 检查 a / g * b 是否会超过 m
                        if a / g > m / b {
                            lcm = m + 1; // 设为大于 m，后续贡献为 0
                            break;
                        }
                        lcm = a / g * b;
                    }
                }
                if lcm <= m {
                    if bits % 2 == 1 {
                        cnt += m / lcm;
                    } else {
                        cnt -= m / lcm;
                    }
                }
            }
            cnt >= k
        };

        // 二分查找最小的 m 使得 check(m) 为真
        let mut left = k - 1;            // 第 k 小至少为 k（当有面额 1 时）
        let mut right = min_coin * k;    // 一个宽松的上界
        while left + 1 < right {
            let mid = left + (right - left) / 2;
            if check(mid) {
                right = mid;
            } else {
                left = mid;
            }
        }
        right
    }
}

/// 最大公约数（欧几里得算法）
fn gcd(a: i64, b: i64) -> i64 {
    if b == 0 { a } else { gcd(b, a % b) }
}
```



示例 1：

输入： coins = [3,6,9], k = 3

输出： 9

解释：给定的硬币可以制造以下金额：
3元硬币产生3的倍数：3, 6, 9, 12, 15等。
6元硬币产生6的倍数：6, 12, 18, 24等。
9元硬币产生9的倍数：9, 18, 27, 36等。
所有硬币合起来可以产生：3, 6, 9, 12, 15等。




示例 2：

输入：coins = [5,2], k = 7

输出：12

解释：给定的硬币可以制造以下金额：
5元硬币产生5的倍数：5, 10, 15, 20等。
2元硬币产生2的倍数：2, 4, 6, 8, 10, 12等。
所有硬币合起来可以产生：2, 4, 5, 6, 8, 10, 12, 14, 15等。
