---
title: "leetcode-数学19"
date: 2026-10-07T20:39:29+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 一周中的第几天

给你一个日期，请你设计一个算法来判断它是对应一周中的哪一天。

输入为三个整数：day、month 和 year，分别表示日、月、年。

您返回的结果必须是这几个值中的一个 {"Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"}。

注意：1971 年 1 月 1 日是星期五。


```
impl Solution {
    /// 返回给定日期是星期几。
    ///
    /// 已知 1971-01-01 是星期五，以其为基准累计天数后对 7 取模即可。
    /// 使用基姆拉尔森公式的等价变形，直接由年月日算出星期。
    pub fn day_of_the_week(day: i32, month: i32, year: i32) -> String {
        const WEEKDAYS: [&str; 7] = [
            "Sunday", "Monday", "Tuesday", "Wednesday",
            "Thursday", "Friday", "Saturday",
        ];

        // 1、2 月视为上一年的 13、14 月，便于统一处理闰年
        let (mut y, mut m) = (year, month);
        if m < 3 {
            m += 12;
            y -= 1;
        }

        // 蔡勒公式（Zeller's congruence）
        let c = y / 100;
        let y = y % 100;
        let mut week = y + y / 4 + c / 4 - 2 * c + 26 * (m + 1) / 10 + day - 1;
        // 处理负数取模，保证落在 0..7
        week = (week % 7 + 7) % 7;

        WEEKDAYS[week as usize].to_string()
    }
}
```
