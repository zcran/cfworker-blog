---
title: "leetcode-动态规划33"
date: 2026-10-05T20:39:56+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 不含连续1的非负整数


给定一个正整数 n ，请你统计在 [0, n] 范围的非负整数中，有多少个整数的二进制表示中不存在 连续的 1 。

```
impl Solution {
    pub fn find_integers(n: i32) -> i32 {
        // memo[pos][state]：
        //   pos   —— 剩余待填的二进制位数
        //   state —— prev * 2 + is_limit，prev 表示前一位是否为 1，is_limit 表示是否受限
        let mut memo = vec![vec![-1i32; 4]; 32];

        fn dfs(pos: usize, prev: usize, is_limit: bool, n: i32, memo: &mut Vec<Vec<i32>>) -> i32 {
            // 所有位已确定，得到一种合法方案
            if pos == 0 {
                return 1;
            }

            let state = prev * 2 + is_limit as usize;
            if memo[pos][state] != -1 {
                return memo[pos][state];
            }

            // 当前位能取的最大值：受限则不超过 n 对应位，否则最大为 1
            let up = if is_limit { (n >> (pos - 1)) & 1 } else { 1 };

            let mut res = 0;
            for d in 0..=up {
                // 不允许出现连续 1
                if prev == 1 && d == 1 {
                    continue;
                }
                res += dfs(pos - 1, d as usize, is_limit && d == up, n, memo);
            }

            memo[pos][state] = res;
            res
        }

        dfs(31, 0, true, n, &mut memo)
    }
}
```
