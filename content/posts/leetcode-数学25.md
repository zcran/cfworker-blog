---
title: "leetcode-数学25"
date: 2026-10-07T20:39:29+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 时钟指针的夹角

给你两个数 hour 和 minutes 。请你返回在时钟上，由给定时间的时针和分针组成的较小角的角度（60 单位制）。


```
impl Solution {
    /// 计算时钟上时针与分针所成较小角的角度（单位：度）。
    ///
    /// - 分针每分钟走 6°（360° / 60）。
    /// - 时针每小时走 30°（360° / 12），且每分钟额外走 0.5°（30° / 60）。
    /// 先求两针夹角，再取其与 360° - 夹角中的较小值。
    pub fn angle_clock(hour: i32, minutes: i32) -> f64 {
        // 分针角度
        let minute_angle = 6.0 * minutes as f64;
        // 时针角度：小时部分 + 分钟带来的偏移
        let hour_angle = 30.0 * (hour % 12) as f64 + 0.5 * minutes as f64;

        let diff = (hour_angle - minute_angle).abs();
        diff.min(360.0 - diff)
    }
}
```
