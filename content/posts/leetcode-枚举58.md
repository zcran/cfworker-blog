---
title: "leetcode-枚举58"
date: 2026-07-09T10:05:02+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 替换字符可以得到的最晚时间

给你一个字符串 s，表示一个 12 小时制的时间格式，其中一些数字（可能没有）被 "?" 替换。

12 小时制时间格式为 "HH:MM" ，其中 HH 的取值范围为 00 至 11，MM 的取值范围为 00 至 59。最早的时间为 00:00，最晚的时间为 11:59。

你需要将 s 中的 所有 "?" 字符替换为数字，使得结果字符串代表的时间是一个 有效 的 12 小时制时间，并且是可能的 最晚 时间。

返回结果字符串。


```
impl Solution {
    pub fn find_latest_time(s: String) -> String {
        let mut chars: Vec<char> = s.chars().collect();

        // 处理小时部分 (HH: 00-11)
        // 第1位：如果是 '?'，尽量填最大值，但不能使小时 > 11
        if chars[0] == '?' {
            chars[0] = if chars[1] == '?' || chars[1] <= '1' { '1' } else { '0' };
        }
        // 第2位：如果是 '?'，在小时首位确定的情况下填最大值
        if chars[1] == '?' {
            chars[1] = if chars[0] == '1' { '1' } else { '9' };
        }

        // 处理分钟部分 (MM: 00-59)
        // 第3位：如果是 '?'，分钟的最大值是 5
        if chars[3] == '?' {
            chars[3] = '5';
        }
        // 第4位：如果是 '?'，分钟的最大值是 9
        if chars[4] == '?' {
            chars[4] = '9';
        }

        chars.into_iter().collect()
    }
}
```
