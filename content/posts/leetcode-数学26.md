---
title: "leetcode-数学26"
date: 2026-10-07T20:39:29+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 日期之间隔几天

请你编写一个程序来计算两个日期之间隔了多少天。

日期以字符串形式给出，格式为 YYYY-MM-DD，如示例所示。


```
impl Solution {
    /// 计算两个日期（格式 `YYYY-MM-DD`）之间相隔的天数。
    ///
    /// 将每个日期转换为一个「从公元元年开始的累计天数」，相减取绝对值即可。
    /// 累计天数公式：先算整年贡献，再算整月贡献，最后加上当月天数。
    pub fn days_between_dates(date1: String, date2: String) -> i32 {
        (Self::to_days(&date1) - Self::to_days(&date2)).abs()
    }

    /// 将 `YYYY-MM-DD` 转换为从公元 1 年 1 月 1 日起算的天数（内部使用偏移公式）。
    fn to_days(date: &str) -> i32 {
        let year = date[0..4].parse::<i32>().unwrap();
        let month = date[5..7].parse::<i32>().unwrap();
        let day = date[8..10].parse::<i32>().unwrap();

        // 1、2 月归入上一年的 13、14 月，便于统一闰年处理
        let (mut y, mut m) = (year, month);
        if m < 3 {
            y -= 1;
            m += 12;
        }

        // 整年天数（含闰年修正） + 整月天数（近似公式） + 当月天数
        (y - 1) * 365 + y / 4 - y / 100 + y / 400
            + (m - 1) * 28 + 13 * (m + 1) / 5 - 7
            + day
    }
}
```
