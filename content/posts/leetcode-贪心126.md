---
title: "leetcode-贪心126"
date: 2026-10-03T12:55:42+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 找出美丽数组的最小和

给你两个正整数：n 和 target 。

如果数组 nums 满足下述条件，则称其为 美丽数组 。

nums.length == n.
nums 由两两互不相同的正整数组成。
在范围 [0, n-1] 内，不存在 两个 不同 下标 i 和 j ，使得 nums[i] + nums[j] == target 。

返回符合条件的美丽数组所可能具备的 最小 和，并对结果进行取模 10^9 + 7。


```
impl Solution {
    pub fn minimum_possible_sum(n: i32, target: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = n as i64;
        let target = target as i64;
        let m = target / 2; // 可安全选取的 1..m 中不含配对和为 target

        // 取最小的正整数：先取 1..m，若不够再从 target 往后取连续的 (n-m) 个
        let sum = if n <= m {
            (1 + n) * n / 2
        } else {
            let cnt = n - m;
            (1 + m) * m / 2 + (target + target + cnt - 1) * cnt / 2
        };

        (sum % MOD) as i32
    }
}
```
