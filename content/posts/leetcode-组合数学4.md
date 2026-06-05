---
title: "leetcode-组合数学4"
date: 2026-05-24T09:54:12+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 丑数 III


丑数是可以被 a 或 b 或 c 整除的 正整数 。

给你四个整数：n 、a 、b 、c ，请你设计一个算法来找出第 n 个丑数。


```
impl Solution {
    pub fn nth_ugly_number(n: i32, a: i32, b: i32, c: i32) -> i32 {
        // 转换为 i64 避免计算过程中溢出
        let (a, b, c, n) = (a as i64, b as i64, c as i64, n as i64);

        // 辅助函数：最大公约数（欧几里得算法）
        fn gcd(mut x: i64, mut y: i64) -> i64 {
            while y != 0 {
                let t = x % y;
                x = y;
                y = t;
            }
            x
        }

        // 辅助函数：最小公倍数
        fn lcm(x: i64, y: i64) -> i64 {
            x / gcd(x, y) * y  // 先除后乘，避免溢出
        }

        // 两两及三者的最小公倍数（用于容斥原理）
        let ab = lcm(a, b);
        let ac = lcm(a, c);
        let bc = lcm(b, c);
        let abc = lcm(ab, c);  // 等价于 lcm(a, lcm(b, c))

        // 检查 mid 是否为第 n 个丑数（含 mid）
        let count_leq = |mid: i64| -> bool {
            // 容斥原理：能被 a,b,c 至少一个整除的数的个数
            let cnt = mid / a + mid / b + mid / c
                    - mid / ab - mid / ac - mid / bc
                    + mid / abc;
            cnt >= n
        };

        // 二分查找最小的 x，使得 <= x 的丑数个数 >= n
        let (mut lo, mut hi) = (1, 2_000_000_000);  // 答案最大不超过 2e9
        while lo < hi {
            let mid = lo + (hi - lo) / 2;  // 防止溢出
            if count_leq(mid as i64) {
                hi = mid;
            } else {
                lo = mid + 1;
            }
        }
        lo as i32
    }
}
```
