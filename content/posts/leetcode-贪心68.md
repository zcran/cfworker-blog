---
title: "leetcode-贪心68"
date: 2026-10-03T12:55:38+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 替换隐藏数字得到的最晚时间

给你一个字符串 time ，格式为  hh:mm（小时：分钟），其中某几位数字被隐藏（用 ? 表示）。

有效的时间为 00:00 到 23:59 之间的所有时间，包括 00:00 和 23:59 。

替换 time 中隐藏的数字，返回你可以得到的最晚有效时间。




```
impl Solution {
    pub fn maximum_time(time: String) -> String {
        let mut t = time.into_bytes(); // 就地修改字节

        // 小时十位：若个位 ≤ 3 或为 '?'，最大取 '2'，否则取 '1'
        if t[0] == b'?' {
            t[0] = if t[1] <= b'3' || t[1] == b'?' { b'2' } else { b'1' };
        }
        // 小时个位：十位为 '2' 时最大 '3'，否则 '9'
        if t[1] == b'?' {
            t[1] = if t[0] == b'2' { b'3' } else { b'9' };
        }
        // 分钟十位最大 '5'
        if t[3] == b'?' {
            t[3] = b'5';
        }
        // 分钟个位最大 '9'
        if t[4] == b'?' {
            t[4] = b'9';
        }

        String::from_utf8(t).unwrap()
    }
}
```
