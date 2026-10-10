---
title: "leetcode-字符串25"
date: 2026-10-10T12:56:52+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 转变日期格式

给你一个字符串 date ，它的格式为 Day Month Year ，其中：

· Day 是集合 {"1st", "2nd", "3rd", "4th", ..., "30th", "31st"} 中的一个元素。
· Month 是集合 {"Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"} 中的一个元素。
· Year 的范围在 ​[1900, 2100] 之间。

请你将字符串转变为 YYYY-MM-DD 的格式，其中：

· YYYY 表示 4 位的年份。
· MM 表示 2 位的月份。
· DD 表示 2 位的天数。


```
impl Solution {
    pub fn reformat_date(date: String) -> String {
        // 月份名 -> 月份数字（1~12）
        const MONTHS: [&str; 12] = [
            "Jan", "Feb", "Mar", "Apr", "May", "Jun",
            "Jul", "Aug", "Sep", "Oct", "Nov", "Dec",
        ];

        // 输入固定为 "Day Month Year" 三段
        let mut parts = date.split(' ');
        let day = parts.next().unwrap();
        let month = parts.next().unwrap();
        let year = parts.next().unwrap();

        // 去掉日期后缀 "st"/"nd"/"rd"/"th"，只留数字
        let day: u32 = day[..day.len() - 2].parse().unwrap();
        // 月份名在数组中的下标 + 1 即月份数字
        let month = MONTHS.iter().position(|&m| m == month).unwrap() + 1;

        // 直接格式化，自动补零
        format!("{}-{:02}-{:02}", year, month, day)
    }
}
```
