---
title: "leetcode-枚举29"
date: 2026-07-09T10:05:00+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 最大波动的子字符串

字符串的 波动 定义为子字符串中出现次数 最多 的字符次数与出现次数 最少 的字符次数之差。

给你一个字符串 s ，它只包含小写英文字母。请你返回 s 里所有 子字符串的 最大波动 值。

子字符串 是一个字符串的一段连续字符序列。


```
use std::collections::HashMap;
use std::cmp::{max, min};

impl Solution {
    pub fn largest_variance(s: String) -> i32 {
        // 1. 记录每个字符出现的所有位置
        let mut pos: HashMap<char, Vec<usize>> = HashMap::new();
        for (i, ch) in s.char_indices() {
            pos.entry(ch).or_insert_with(Vec::new).push(i);
        }

        let mut ans = 0;

        // 2. 枚举字符对 (c0, c1)，c0 作为"多数"，c1 作为"少数"
        for (&c0, pos0) in &pos {
            for (&c1, pos1) in &pos {
                if c0 == c1 {
                    continue;
                }

                let mut i = 0; // pos0 的指针
                let mut j = 0; // pos1 的指针
                let mut f = 0; // 以当前字符结尾的、包含 c0 和 c1 的"净收益"
                let mut g = i32::MIN; // 当前最大波动值

                // 3. 合并遍历两个位置数组，就像遍历原字符串中只包含 c0/c1 的序列
                while i < pos0.len() || j < pos1.len() {
                    // 判断下一个出现的字符是 c0 还是 c1
                    let is_c0 = j == pos1.len() || (i < pos0.len() && pos0[i] < pos1[j]);

                    if is_c0 {
                        // 遇到 c0：贡献 +1
                        f = max(f, 0) + 1; // f 可以重置为 0（从当前位置开始新子串）
                        g = g + 1; // 当前波动值增加
                        i += 1;
                    } else {
                        // 遇到 c1：贡献 -1
                        // g = max(f, max(g, 0)) - 1 的含义：
                        // - max(g, 0)：允许从当前位置重置开始新子串
                        // - max(f, ...)：选择之前的 f（延续）或重置
                        g = max(f, max(g, 0)) - 1;
                        f = max(f, 0) - 1; // f 也相应减少
                        j += 1;
                    }

                    // 4. 更新全局最大波动
                    ans = max(ans, g);
                }
            }
        }

        ans
    }
}
```
