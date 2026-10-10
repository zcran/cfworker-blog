---
title: "leetcode-数学17"
date: 2026-10-07T20:39:29+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 一年中的第几天

给你一个字符串 date ，按 YYYY-MM-DD 格式表示一个 现行公元纪年法 日期。返回该日期是当年的第几天。


```
impl Solution {
    /// 返回日期 `YYYY-MM-DD` 是当年的第几天。
    ///
    /// 累加该月之前各月的天数，加上当月天数；
    /// 若为闰年且月份 ≥ 3，则额外加 1（2 月有 29 天）。
    /// 闰年判定：能被 400 整除，或能被 4 整除但不能被 100 整除。
    pub fn day_of_year(date: String) -> i32 {
        // 各月天数（2 月按平年 28 天计）
        const MONTH_DAYS: [i32; 12] = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31];

        // 解析年、月、日（格式固定为 YYYY-MM-DD，直接按字节切片避免分配）
        let b = date.as_bytes();
        let year = date[0..4].parse::<i32>().unwrap();
        let month = date[5..7].parse::<i32>().unwrap();
        let day = date[8..10].parse::<i32>().unwrap();
        let _ = b; // 保留字节视图，可读性说明

        // 累加该月之前的天数
        let mut total: i32 = MONTH_DAYS[..(month - 1) as usize].iter().sum();

        // 闰年且月份 ≥ 3 时补 1 天
        let is_leap = year % 400 == 0 || (year % 4 == 0 && year % 100 != 0);
        if is_leap && month >= 3 {
            total += 1;
        }

        total + day
    }
}
```
