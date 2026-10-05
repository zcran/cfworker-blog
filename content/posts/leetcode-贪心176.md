---
title: "leetcode-贪心176"
date: 2026-10-03T12:55:46+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 最大总价值

给你两个整数数组 value 和 decay，以及一个整数 m。

value[i] 表示下标 i 的初始价值。
decay[i] 表示每次选择下标 i 后，该下标的价值会减少的数值。

你可以多次 选择 任意下标。所有下标的总选择次数不得超过 m。

如果重复选择下标 i，第 t 次（从 1 开始计数）获得的价值为 value[i] - decay[i] * (t - 1)。

返回你能够获得的 最大 总价值。由于答案可能很大，请返回其对 10^9 + 7 取模后的结果。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn max_total_value(value: Vec<i32>, decay: Vec<i32>, m: i32) -> i32 {
        let n = value.len();
        let m = m as i64;

        // check(low)：能否选出超过 m 次、每次价值都 >= low 的选择
        // 对每个下标 i，价值 >= low 的选择次数为 (value[i]-low)/decay[i] + 1
        let check = |low: i32| -> bool {
            let mut left = m;
            for i in 0..n {
                let v = value[i];
                if v >= low {
                    left -= ((v - low) / decay[i] + 1) as i64;
                    if left < 0 {
                        return true;
                    }
                }
            }
            false
        };

        // 二分找到阈值 low：使得价值 >= low 的选择次数刚好 <= m
        // 若 check(0) 为 false，说明所有能选的次数不足 m，直接取 low = 0
        let mut low = 0;
        if check(0) {
            let (mut lo, mut hi) = (0, *value.iter().max().unwrap() + 1);
            while lo + 1 < hi {
                let mid = lo + (hi - lo) / 2;
                if check(mid) {
                    lo = mid;
                } else {
                    hi = mid;
                }
            }
            low = lo;
        }

        // 累加所有价值严格大于 low 的选择
        let mut ans = 0i64;
        let mut remaining = m;
        for i in 0..n {
            let v = value[i] as i64;
            let d = decay[i] as i64;
            if (value[i] as i32) > low {
                // 价值 > low 的选择次数 k
                let k = (v - low as i64 - 1) / d + 1;
                remaining -= k;
                // 等差数列求和：首项 v，公差 -d，共 k 项，求和公式 (v + (v - d*(k-1))) * k / 2
                ans += (v * 2 - d * (k - 1)) * k;
            }
        }
        ans /= 2;

        // 剩余 remaining 次选择的价值都恰为 low
        ans += remaining * low as i64;

        (ans % MOD) as i32
    }
}
```
