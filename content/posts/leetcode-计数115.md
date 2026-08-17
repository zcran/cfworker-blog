---
title: "leetcode-计数115"
date: 2026-08-01T10:40:57+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 不同字符数量最多为 K 时的最少删除数

给你一个字符串 s（由小写英文字母组成）和一个整数 k。

你的任务是删除字符串中的一些字符（可以不删除任何字符），使得结果字符串中的 不同字符数量 最多为 k。

返回为达到上述目标所需删除的 最小 字符数量。


```
impl Solution {
    /// 删除最少数量的字符，使得剩余字符串中不同字符种类数不超过 k。
    /// 策略：统计每种字符频率，保留频率最高的 k 种，删除其余。
    pub fn min_deletion(s: String, k: i32) -> i32 {
        let k = k as usize;
        if k >= 26 {
            return 0;
        }

        let mut freq = [0u32; 26];
        for &b in s.as_bytes() {
            freq[(b - b'a') as usize] += 1;
        }

        freq.sort_unstable();
        let keep: u32 = freq.iter().skip(26 - k).sum();
        s.len() as i32 - keep as i32
    }
}
```
