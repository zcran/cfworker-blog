---
title: "leetcode-回溯46"
date: 2026-07-04T10:22:03+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 长度为 n 的开心字符串中字典序第 k 小的字符串

一个 「开心字符串」定义为：

· 仅包含小写字母 ['a', 'b', 'c'].
· 对所有在 1 到 s.length - 1 之间的 i ，满足 s[i] != s[i + 1] （字符串的下标从 1 开始）。

比方说，字符串 "abc"，"ac"，"b" 和 "abcbabcbcb" 都是开心字符串，但是 "aa"，"baa" 和 "ababbc" 都不是开心字符串。

给你两个整数 n 和 k ，你需要将长度为 n 的所有开心字符串按字典序排序。

请你返回排序后的第 k 个开心字符串，如果长度为 n 的开心字符串少于 k 个，那么请你返回 空字符串 。


```
impl Solution {
    pub fn get_happy_string(n: i32, mut k: i32) -> String {
        let n = n as usize;
        // 总数量 = 3 * 2^(n-1)，如果 k 超出范围直接返回空
        if k > 3 * (1 << (n - 1)) {
            return String::new();
        }

        let chars = ['a', 'b', 'c'];
        let mut result = String::with_capacity(n);

        // 逐位构造
        for i in 0..n {
            // 当前位确定后，剩余位可能的组合数 = 2^(n-i-1)
            let count = 1 << (n - i - 1);

            for &ch in &chars {
                // 不能与上一个字符相同
                if !result.is_empty() && result.as_bytes()[result.len() - 1] == ch as u8 {
                    continue;
                }

                if k <= count {
                    // 第 k 个在当前分支中
                    result.push(ch);
                    break;
                } else {
                    // 跳过整个分支
                    k -= count;
                }
            }
        }

        result
    }
}
```
