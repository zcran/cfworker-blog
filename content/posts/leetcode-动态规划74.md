---
title: "leetcode-动态规划74"
date: 2026-10-05T20:39:59+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 停在原地的方案数

有一个长度为 arrLen 的数组，开始有一个指针在索引 0 处。

每一步操作中，你可以将指针向左或向右移动 1 步，或者停在原地（指针不能被移动到数组范围外）。

给你两个整数 steps 和 arrLen ，请你计算并返回：在恰好执行 steps 次操作以后，指针仍然指向索引 0 处的方案数。

由于答案可能会很大，请返回方案数 模 10^9 + 7 后的结果。


```
impl Solution {
    /// 1269. 停在原地的方案数
    ///
    /// dp[p]：当前步数下，指针停在位置 p 的方案数。
    /// 每走一步，新方案由"原地不动、从 p-1 右移、从 p+1 左移"三部分相加：
    ///   new_dp[p] = dp[p] + dp[p-1] + dp[p+1]
    ///
    /// 关键剪枝：执行 steps 步后要回到 0，指针最多只能走到 steps/2 的位置
    /// （再远就必须花更多步返回，无法归零），故数组只需开到 min(arrLen-1, steps/2)。
    /// 这样空间 O(min(arrLen, steps))，时间 O(steps * min(arrLen, steps))。
    pub fn num_ways(steps: i32, arr_len: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let steps = steps as usize;
        // 最远能到达并返回 0 的位置
        let max_pos = ((arr_len - 1) as usize).min(steps / 2) + 1;

        let mut dp = vec![0i64; max_pos];
        dp[0] = 1;

        for _ in 0..steps {
            let mut next = vec![0i64; max_pos];
            for p in 0..max_pos {
                let mut v = dp[p]; // 原地不动
                if p > 0 {
                    v += dp[p - 1]; // 从 p-1 右移过来
                }
                if p + 1 < max_pos {
                    v += dp[p + 1]; // 从 p+1 左移过来
                }
                next[p] = v % MOD;
            }
            dp = next;
        }

        dp[0] as i32
    }
}
```
