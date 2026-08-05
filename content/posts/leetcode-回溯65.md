---
title: "leetcode-回溯65"
date: 2026-07-04T10:22:04+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 将字符串分割为最少的美丽子字符串

给你一个二进制字符串 s ，你需要将字符串分割成一个或者多个 子字符串  ，使每个子字符串都是 美丽 的。

如果一个字符串满足以下条件，我们称它是 美丽 的：

它不包含前导 0 。

它是 5 的幂的 二进制 表示。

请你返回分割后的子字符串的 最少 数目。如果无法将字符串 s 分割成美丽子字符串，请你返回 -1 。

子字符串是一个字符串中一段连续的字符序列。


```
impl Solution {
    pub fn minimum_beautiful_substrings(s: String) -> i32 {
        let s = s.as_bytes();
        let n = s.len();

        // 所有 5 的幂（2^0 到 2^15，因为 s 长度最多 15）
        // 对应十进制：1, 5, 25, 125, 625, 3125, 15625, 78125, 390625, ...
        // 二进制：1, 101, 11001, 1111101, 1001110001, ...
        const POWERS: [&str; 7] = [
            "1", "101", "11001", "1111101", "1001110001",
            "110000110101", "11110100001001"
        ];
        // 将幂转换为字节数组以便快速比较
        const POWER_BYTES: [&[u8]; 7] = [
            b"1", b"101", b"11001", b"1111101", b"1001110001",
            b"110000110101", b"11110100001001"
        ];

        // 判断子串是否为美丽字符串
        fn is_beautiful(start: usize, end: usize, s: &[u8]) -> bool {
            // 不能有前导 0
            if s[start] == b'0' {
                return false;
            }
            let len = end - start;
            // 与所有 5 的幂比较
            for &power in POWER_BYTES.iter() {
                if power.len() == len && &s[start..end] == power {
                    return true;
                }
            }
            false
        }

        // 记忆化搜索：memo[i] 表示从 i 到末尾的最小分割数
        let mut memo = vec![-1; n];

        fn dfs(
            start: usize,
            s: &[u8],
            memo: &mut Vec<i32>,
            n: usize,
        ) -> i32 {
            if start >= n {
                return 0;
            }
            if memo[start] != -1 {
                return memo[start];
            }

            let mut min_parts = i32::MAX;

            // 枚举所有可能的美丽子串
            for end in start + 1..=n {
                if is_beautiful(start, end, s) {
                    let rest = dfs(end, s, memo, n);
                    if rest != -1 {
                        min_parts = min_parts.min(1 + rest);
                    }
                }
            }

            memo[start] = if min_parts == i32::MAX { -1 } else { min_parts };
            memo[start]
        }

        dfs(0, s, &mut memo, n)
    }
}
```
