---
title: "leetcode-模拟138"
date: 2026-08-08T11:31:17+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 交通信号灯的颜色

给你一个整数 timer，表示交通信号灯上的剩余时间（以秒为单位）。

信号灯遵循以下规则：

如果 timer == 0，信号灯为 "Green"
如果 timer == 30，信号灯为 "Orange"
如果 30 < timer <= 90，信号灯为 "Red"

返回信号灯的当前状态。如果均不满足上述条件，返回 "Invalid"。


```
impl Solution {
    /// 根据剩余时间判断信号灯状态
    pub fn traffic_signal(timer: i32) -> String {
        String::from(match timer {
            0 => "Green",
            30 => "Orange",
            31..=90 => "Red",
            _ => "Invalid",
        })
    }
}
```
