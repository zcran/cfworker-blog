---
title: "leetcode-贪心99"
date: 2026-10-03T12:55:40+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 转化时间需要的最少操作数

给你两个字符串 current 和 correct ，表示两个 24 小时制时间 。

24 小时制时间 按 "HH:MM" 进行格式化，其中 HH 在 00 和 23 之间，而 MM 在 00 和 59 之间。最早的 24 小时制时间为 00:00 ，最晚的是 23:59 。

在一步操作中，你可以将 current 这个时间增加 1、5、15 或 60 分钟。你可以执行这一操作 任意 次数。

返回将 current 转化为 correct 需要的 最少操作数 。


```
impl Solution {
    pub fn convert_time(current: String, correct: String) -> i32 {
        // 将 "HH:MM" 解析为一天中的分钟数
        let parse = |s: &str| -> i32 {
            let b = s.as_bytes();
            let hh = (b[0] - b'0') as i32 * 10 + (b[1] - b'0') as i32;
            let mm = (b[3] - b'0') as i32 * 10 + (b[4] - b'0') as i32;
            hh * 60 + mm
        };

        let cur = parse(&current);
        let mut diff = parse(&correct) - cur;
        if diff < 0 {
            diff += 24 * 60; // 跨天，绕回第二天
        }

        // 贪心：优先用大面额（60、15、5、1）换取最少操作数
        let mut ans = 0;
        for step in [60, 15, 5, 1] {
            ans += diff / step;
            diff %= step;
        }
        ans
    }
}
```
