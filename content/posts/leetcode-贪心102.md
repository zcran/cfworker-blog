---
title: "leetcode-贪心102"
date: 2026-10-03T12:55:40+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 不可能得到的最短骰子序列

给你一个长度为 n 的整数数组 rolls 和一个整数 k 。你扔一个 k 面的骰子 n 次，骰子的每个面分别是 1 到 k ，其中第 i 次扔得到的数字是 rolls[i] 。

请你返回 无法 从 rolls 中得到的 最短 骰子子序列的长度。

扔一个 k 面的骰子 len 次得到的是一个长度为 len 的 骰子子序列 。

注意 ，子序列只需要保持在原数组中的顺序，不需要连续。




```
impl Solution {
    pub fn shortest_sequence(rolls: Vec<i32>, k: i32) -> i32 {
        // 贪心：只要某段区间内 1..=k 全部出现过，就能构成一次完整覆盖，
        // 记录完整覆盖的次数，最终答案即为覆盖次数 + 1。
        let mut seen = vec![false; k as usize + 1];
        let mut count = 0;   // 当前段内已出现的不同数字个数
        let mut groups = 0;  // 完整覆盖次数

        for r in rolls {
            let idx = r as usize;
            if !seen[idx] {
                seen[idx] = true;
                count += 1;
                if count == k {
                    // 收集齐了一整套，开启新的一段
                    seen = vec![false; k as usize + 1];
                    count = 0;
                    groups += 1;
                }
            }
        }
        groups + 1
    }
}
```
