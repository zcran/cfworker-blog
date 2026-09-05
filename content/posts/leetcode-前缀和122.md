---
title: "leetcode-前缀和122"
date: 2026-08-25T10:02:53+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 最长的平衡子串 II

给你一个只包含字符 'a'、'b' 和 'c' 的字符串 s。

如果一个 子串 中所有 不同 字符出现的次数都 相同，则称该子串为 平衡 子串。

请返回 s 的 最长平衡子串 的 长度 。

子串 是字符串中连续的、非空 的字符序列。


```
use std::collections::HashMap;

impl Solution {
    pub fn longest_balanced(s: String) -> i32 {
        let s = s.as_bytes();
        let n = s.len();
        let mut ans = 0;

        // 情况一：只包含一种字符（连续相同字符的最长长度）
        let mut cnt = 1;
        for i in 1..n {
            if s[i] == s[i - 1] {
                cnt += 1;
            } else {
                ans = ans.max(cnt);
                cnt = 1;
            }
        }
        ans = ans.max(cnt); // 处理末尾

        // 辅助函数：处理两种字符的情况
        let mut two_char = |x: u8, y: u8| {
            let mut map = HashMap::new();
            map.insert(0, -1);
            let mut diff = 0;
            for i in 0..n {
                let ch = s[i];
                if ch != x && ch != y {
                    // 遇到无关字符，重置状态
                    map.clear();
                    map.insert(0, i as i32); // 从下一个位置开始
                    diff = 0;
                    continue;
                }
                if ch == x {
                    diff += 1;
                } else {
                    diff -= 1;
                }
                if let Some(&prev) = map.get(&diff) {
                    ans = ans.max(i as i32 - prev);
                } else {
                    map.insert(diff, i as i32);
                }
            }
        };

        // 情况二：两种字符的情况
        two_char(b'a', b'b');
        two_char(b'b', b'c');
        two_char(b'a', b'c');

        // 情况三：三种字符都出现，要求 cnt[a] == cnt[b] == cnt[c]
        // 等价于 cnt[b] - cnt[a] == 0 且 cnt[b] - cnt[c] == 0
        let encode = |d1: i32, d2: i32| -> i64 {
            // 差值范围 [-n, n]，使用 2n+1 作为基数
            (d1 + n as i32) as i64 * (2 * n as i64 + 1) + (d2 + n as i32) as i64
        };

        let mut counts = [0; 3];
        let mut map = HashMap::new();
        map.insert(encode(0, 0), -1);
        for i in 0..n {
            counts[(s[i] - b'a') as usize] += 1;
            let key = encode(counts[1] - counts[0], counts[1] - counts[2]);
            if let Some(&prev) = map.get(&key) {
                ans = ans.max(i as i32 - prev);
            } else {
                map.insert(key, i as i32);
            }
        }

        ans
    }
}
```
