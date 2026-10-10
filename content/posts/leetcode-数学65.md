---
title: "leetcode-数学65"
date: 2026-10-07T20:39:32+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 将日期转换为二进制表示

给你一个字符串 date，它的格式为 yyyy-mm-dd，表示一个公历日期。

date 可以重写为二进制表示，只需要将年、月、日分别转换为对应的二进制表示（不带前导零）并遵循 year-month-day 的格式。

返回 date 的 二进制 表示。


```
impl Solution {
    pub fn convert_date_to_binary(date: String) -> String {
        // 按 "yyyy-mm-dd" 固定格式切片解析出年、月、日，
        // 分别转成不带前导零的二进制，再用 '-' 连接。
        let year = date[0..4].parse::<i32>().unwrap();
        let month = date[5..7].parse::<i32>().unwrap();
        let day = date[8..10].parse::<i32>().unwrap();

        format!("{year:b}-{month:b}-{day:b}")
    }
}
```
