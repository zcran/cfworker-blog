---
title: "leetcode-数学50"
date: 2026-10-07T20:39:31+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 计算列车到站时间

给你一个正整数 arrivalTime 表示列车正点到站的时间（单位：小时），另给你一个正整数 delayedTime 表示列车延误的小时数。

返回列车实际到站的时间。

注意，该问题中的时间采用 24 小时制。


```
impl Solution {
    pub fn find_delayed_arrival_time(arrival_time: i32, delayed_time: i32) -> i32 {
        // 实际到站 = 正点 + 延误，再对 24 取模换算到 24 小时制
        (arrival_time + delayed_time) % 24
    }
}
```
