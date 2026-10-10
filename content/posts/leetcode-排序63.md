---
title: "leetcode-排序63"
date: 2026-10-05T00:35:03+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 高访问员工

给你一个长度为 n 、下标从 0 开始的二维字符串数组 access_times 。对于每个 i（0 <= i <= n - 1 ），access_times[i][0] 表示某位员工的姓名，access_times[i][1] 表示该员工的访问时间。access_times 中的所有条目都发生在同一天内。

访问时间用 四位 数字表示， 符合 24 小时制 ，例如 "0800" 或 "2250" 。

如果员工在 同一小时内 访问系统 三次或更多 ，则称其为 高访问 员工。

时间间隔正好相差一小时的时间 不 被视为同一小时内。例如，"0815" 和 "0915" 不属于同一小时内。

一天开始和结束时的访问时间不被计算为同一小时内。例如，"0005" 和 "2350" 不属于同一小时内。

以列表形式，按任意顺序，返回所有 高访问 员工的姓名。


```
use std::collections::HashMap;

impl Solution {
    pub fn find_high_access_employees(access_times: Vec<Vec<String>>) -> Vec<String> {
        // 将 "HHMM" 解析为当天的分钟数，便于比较是否在同一小时内
        fn parse_minutes(s: &str) -> i32 {
            let b = s.as_bytes();
            let hh = (b[0] - b'0') as i32 * 10 + (b[1] - b'0') as i32;
            let mm = (b[2] - b'0') as i32 * 10 + (b[3] - b'0') as i32;
            hh * 60 + mm
        }

        // 按员工姓名分组，收集其所有访问时间（分钟数）
        let mut record: HashMap<&str, Vec<i32>> = HashMap::new();
        for entry in &access_times {
            record
                .entry(entry[0].as_str())
                .or_default()
                .push(parse_minutes(&entry[1]));
        }

        // 对每位员工的时间排序后，检查是否存在连续三次访问落在同一小时内
        let mut result = Vec::new();
        for (name, mut times) in record {
            if times.len() < 3 {
                continue;
            }
            times.sort_unstable();
            if times.windows(3).any(|w| w[2] - w[0] < 60) {
                result.push(name.to_owned());
            }
        }

        result
    }
}
```
