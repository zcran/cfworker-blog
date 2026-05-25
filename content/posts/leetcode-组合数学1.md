---
title: "leetcode-组合数学1"
date: 2026-05-24T09:54:12+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 不同路径


一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为 “Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。

问总共有多少条不同的路径？


```
impl Solution {
    pub fn unique_paths(m: i32, n: i32) -> i32 {
        let m = m as usize;
        let n = n as usize;
        let mut dp = vec![1;n];

        for i in 1..m {
            dp[0] = 1;
            for j in 1..n {
                dp[j] += dp[j-1];
            }
        }

        dp[n-1]
    }
}
```
