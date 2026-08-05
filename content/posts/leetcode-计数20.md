---
title: "leetcode-计数20"
date: 2026-08-01T10:40:51+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 制造字母异位词的最小步骤数

给你两个长度相等的字符串 s 和 t。每一个步骤中，你可以选择将 t 中的 任一字符 替换为 另一个字符。

返回使 t 成为 s 的字母异位词的最小步骤数。

字母异位词 指字母相同，但排列不同（也可能相同）的字符串。


```
impl Solution {
    pub fn min_steps(s: String, t: String) -> i32 {
        // 统计 s 中每个字母的出现次数
        let mut count_s = [0; 26];
        for ch in s.bytes() {
            count_s[(ch - b'a') as usize] += 1;
        }

        // 统计 t 中每个字母的出现次数，并累加多余的字母数
        let mut count_t = [0; 26];
        let mut steps = 0;

        for ch in t.bytes() {
            let idx = (ch - b'a') as usize;
            count_t[idx] += 1;

            // 如果 t 中某字母出现次数超过 s 中该字母的次数，需要替换这些多余的
            // 但注意：不能在这里直接累加，因为可能存在后续字母补偿的情况
            // 正确做法：先统计完全部，再计算差值
        }

        // 计算 t 中每个字母超出 s 的部分，这些就是需要替换的最小步数
        for i in 0..26 {
            if count_t[i] > count_s[i] {
                steps += count_t[i] - count_s[i];
            }
        }

        steps
    }
}
```
