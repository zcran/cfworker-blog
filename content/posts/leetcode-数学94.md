---
title: "leetcode-数学94"
date: 2026-10-07T20:39:34+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 统计起止时间经过的秒数

给你两个有效时间 startTime 和 endTime，它们均以字符串形式表示，格式为 "HH:MM:SS"。

返回从 startTime 到 endTime 经过的秒数（包含两个端点）。


```
impl Solution {
    pub fn seconds_between_times(start_time: String, end_time: String) -> i32 {
        // 将 "HH:MM:SS" 解析为从 00:00:00 起算的秒数
        // 注意题目说包含两个端点，但这是计算经过的时间差，因此直接用 end - start
        fn parse(t: &[u8]) -> i32 {
            let h = (t[0] - b'0') as i32 * 10 + (t[1] - b'0') as i32;
            let m = (t[3] - b'0') as i32 * 10 + (t[4] - b'0') as i32;
            let s = (t[6] - b'0') as i32 * 10 + (t[7] - b'0') as i32;
            h * 3600 + m * 60 + s
        }

        parse(end_time.as_bytes()) - parse(start_time.as_bytes())
    }
}
```
