---
title: "leetcode-动态规划49"
date: 2026-10-05T20:39:57+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 赛车

你的赛车可以从位置 0 开始，并且速度为 +1 ，在一条无限长的数轴上行驶。赛车也可以向负方向行驶。赛车可以按照由加速指令 'A' 和倒车指令 'R' 组成的指令序列自动行驶。
· 当收到指令 'A' 时，赛车这样行驶：
    · position += speed
    · speed *= 2
· 当收到指令 'R' 时，赛车这样行驶：
    · 如果速度为正数，那么speed = -1
    · 否则 speed = 1
当前所处位置不变。

例如，在执行指令 "AAR" 后，赛车位置变化为 0 --> 1 --> 3 --> 3 ，速度变化为 1 --> 2 --> 4 --> -1 。

给你一个目标位置 target ，返回能到达目标位置的最短指令序列的长度。


```
impl Solution {
    /// 818. 赛车
    ///
    /// dp[t] 表示到达位置 t 的最短指令长度。
    /// 对于每个 t，令 n 为满足 2^n - 1 >= t 的最小整数，a = 2^n - 1。
    /// 若 a == t，则 dp[t] = n。
    /// 否则有两种策略：
    /// 1. 先连续 n 个 'A' 到达 a，然后 'R' 倒车，再走 a - t 的距离：
    ///    dp[t] = min(dp[t], n + 1 + dp[a - t])
    /// 2. 先连续 n-1 个 'A' 到达 half = 2^(n-1) - 1，然后 'R'，
    ///    再连续 i 个 'A' 回退，再 'R' 转为正向前进，走完剩余距离：
    ///    dp[t] = min(dp[t], dp[t - half + (2^i - 1)] + (n-1) + 1 + 1 + i)
    pub fn racecar(target: i32) -> i32 {
        let target = target as usize;
        let mut dp = vec![i32::MAX; target + 1];
        dp[0] = 0;

        for t in 1..=target {
            // n = ceil(log2(t + 1))，即满足 2^n - 1 >= t 的最小 n
            let mut n = 0;
            while (1usize << n) - 1 < t {
                n += 1;
            }
            let a = (1usize << n) - 1;

            if a == t {
                dp[t] = n as i32;
                continue;
            }

            // 策略 1：冲到 a，倒车，再走到 a - t
            dp[t] = dp[t].min(n as i32 + 1 + dp[a - t]);

            // 策略 2：先到 half，倒车回退 i 步，再转向前进
            let half = (1usize << (n - 1)) - 1;
            for i in 0..n {
                let back = (1usize << i) - 1;
                let remaining = t - half + back;
                if remaining < t {
                    dp[t] = dp[t].min(
                        dp[remaining] + (n as i32 - 1) + 1 + 1 + i as i32
                    );
                }
            }
        }

        dp[target]
    }
}
```
