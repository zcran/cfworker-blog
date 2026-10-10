---
title: "leetcode-动态规划111"
date: 2026-10-05T20:40:01+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 获得分数的方法数

考试中有 n 种类型的题目。给你一个整数 target 和一个下标从 0 开始的二维整数数组 types ，其中 types[i] = [counti, marksi] 表示第 i 种类型的题目有 counti 道，每道题目对应 marksi 分。

返回你在考试中恰好得到 target 分的方法数。由于答案可能很大，结果需要对 10^9 +7 取余。

注意，同类型题目无法区分。

比如说，如果有 3 道同类型题目，那么解答第 1 和第 2 道题目与解答第 1 和第 3 道题目或者第 2 和第 3 道题目是相同的。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn ways_to_reach_target(target: i32, types: Vec<Vec<i32>>) -> i32 {
        let target = target as usize;
        // dp[j]: 凑出恰好 j 分的方法数
        let mut dp = vec![0i64; target + 1];
        dp[0] = 1;

        for t in &types {
            let count = t[0] as usize;
            let mark = t[1] as usize;
            // 滑动窗口优化多重背包：按 mark 分同余类，维护窗口内 dp 之和
            let mut ndp = vec![0i64; target + 1];

            // 遍历余数 r（只需考虑可能到达的 r）
            for r in 0..mark.min(target + 1) {
                let mut sum = 0i64;
                let mut j = r;
                let mut left = r; // 窗口左端，初始为 r
                while j <= target {
                    sum = (sum + dp[j]) % MOD;
                    // 若窗口内元素个数超过 count+1（即多选了超过 count 道题），移除最旧的值
                    if (j - r) / mark > count {
                        sum = (sum - dp[left] + MOD) % MOD;
                        left += mark;
                    }
                    ndp[j] = sum;
                    j += mark;
                }
            }
            dp = ndp;
        }

        dp[target] as i32
    }
}
```
