---
title: "leetcode-计数107"
date: 2026-08-01T10:40:56+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 字符串转换后的长度 I

给你一个字符串 s 和一个整数 t，表示要执行的 转换 次数。每次 转换 需要根据以下规则替换字符串 s 中的每个字符：

如果字符是 'z'，则将其替换为字符串 "ab"。

否则，将其替换为字母表中的下一个字符。例如，'a' 替换为 'b'，'b' 替换为 'c'，依此类推。

返回 恰好 执行 t 次转换后得到的字符串的 长度。

由于答案可能非常大，返回其对 10^9 + 7 取余的结果。


```
impl Solution {
    pub fn length_after_transformations(s: String, t: i32) -> i32 {
        const MOD: i32 = 1_000_000_007;
        let mut cnt = [0; 26];

        // 统计初始字符频率
        for b in s.bytes() {
            cnt[(b - b'a') as usize] = (cnt[(b - b'a') as usize] + 1) % MOD;
        }

        // 模拟 t 次转换
        // 转移规则：'z' -> "ab"（z的数量同时给a和b）
        // 其他字符 -> 下一个字符（cnt[i] 给 cnt[i+1]）
        for _ in 0..t {
            let mut nxt = [0; 26];
            nxt[0] = cnt[25];                           // z -> a
            nxt[1] = (cnt[25] + cnt[0]) % MOD;          // z -> b, 且 a -> b
            for i in 2..26 {
                nxt[i] = cnt[i - 1];                    // b->c, c->d, ..., y->z
            }
            cnt = nxt;
        }

        cnt.iter().fold(0, |acc, &x| (acc + x) % MOD)
    }
}
```
