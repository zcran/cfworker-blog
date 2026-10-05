---
title: "leetcode-贪心59"
date: 2026-10-03T12:55:38+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 使绳子变成彩色的最短时间

Alice 把 n 个气球排列在一根绳子上。给你一个下标从 0 开始的字符串 colors ，其中 colors[i] 是第 i 个气球的颜色。

Alice 想要把绳子装扮成 五颜六色的 ，且她不希望两个连续的气球涂着相同的颜色，所以她喊来 Bob 帮忙。Bob 可以从绳子上移除一些气球使绳子变成 彩色 。给你一个 下标从 0 开始 的整数数组 neededTime ，其中 neededTime[i] 是 Bob 从绳子上移除第 i 个气球需要的时间（以秒为单位）。

返回 Bob 使绳子变成 彩色 需要的 最少时间 。


```
impl Solution {
    pub fn min_cost(colors: String, needed_time: Vec<i32>) -> i32 {
        let bytes = colors.as_bytes();
        let mut total = 0;
        let mut i = 0;

        // 按连续相同颜色的区段处理：每段保留耗时最大的一个，其余全部移除
        while i < bytes.len() {
            let color = bytes[i];
            let mut max_time = 0;
            let mut sum = 0;

            while i < bytes.len() && bytes[i] == color {
                sum += needed_time[i];
                max_time = max_time.max(needed_time[i]);
                i += 1;
            }
            total += sum - max_time;
        }
        total
    }
}
```
