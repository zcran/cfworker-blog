---
title: "leetcode-位掩码34"
date: 2026-05-18T10:28:00+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 使数组包含目标值倍数的最少增量


给你两个数组 nums 和 target 。

在一次操作中，你可以将 nums 中的任意一个元素递增 1 。

返回要使 target 中的每个元素在 nums 中 至少 存在一个倍数所需的 最少操作次数 。

```
impl Solution {
    /// 返回使每个 target 都能被某个 nums 中的数整除所需的最小总增量。
    /// 每个 nums 中的数最多只能用一次。
    pub fn minimum_increments(nums: Vec<i32>, mut target: Vec<i32>) -> i32 {
        // 1. 预处理 target：移除冗余的数（若大数能整除小数，则小数可被大数覆盖）
        target.sort_unstable_by(|a, b| b.cmp(a)); // 降序
        let mut uniq = Vec::new();
        for &t in &target {
            if !uniq.iter().any(|&r| r % t == 0) {
                uniq.push(t);
            }
        }
        let target = uniq;
        let n = target.len();
        if n == 0 {
            return 0;
        }

        // 2. 预计算所有子集的最小公倍数（LCM）
        let size = 1 << n;
        let mut lcm = vec![1_i64; size];
        for mask in 1..size {
            let lsb = mask & mask.wrapping_neg(); // 最低位的 1
            let idx = lsb.trailing_zeros() as usize;
            let prev = mask ^ lsb;
            let a = target[idx] as i64;
            let b = lcm[prev];
            lcm[mask] = if prev == 0 {
                a
            } else {
                let g = Self::gcd(a, b);
                (a / g) * b // 先除后乘防止中间溢出
            };
        }

        // 3. 动态规划：dp[mask] 表示覆盖 mask 子集所需的最小增量（i64 防止溢出）
        let inf = i64::MAX;
        let mut dp = vec![inf; size];
        dp[0] = 0;

        for &num in &nums {
            let mut new_dp = dp.clone(); // 不选当前数的情况
            let x = num as i64;
            for mask in 1..size {
                // 枚举 mask 的所有非空子集 sub
                let mut sub = mask;
                while sub != 0 {
                    let l = lcm[sub];
                    let rem = x % l;
                    let cost = if rem == 0 { 0 } else { l - rem };
                    let prev = mask ^ sub;
                    if dp[prev] != inf {
                        let candidate = dp[prev] + cost;
                        if candidate < new_dp[mask] {
                            new_dp[mask] = candidate;
                        }
                    }
                    sub = (sub - 1) & mask;
                }
            }
            dp = new_dp;
        }

        let ans = dp[size - 1];
        if ans >= inf {
            -1
        } else {
            ans as i32
        }
    }

    /// 最大公约数（欧几里得算法，函数式递归）
    fn gcd(a: i64, b: i64) -> i64 {
        if b == 0 { a } else { Self::gcd(b, a % b) }
    }
}
```
