---
title: "leetcode-滚动哈希2"
date: 2026-04-17T20:00:31+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


## 最短回文串

给定一个字符串 s，你可以通过在字符串前面添加字符将其转换为回文串。找到并返回可以用这种方式转换的最短回文串。

```
impl Solution {
    /// 返回通过在字符串前面添加字符得到的最短回文串。
    ///
    /// # 原理
    /// 1. 寻找最长的回文前缀 `s[0..len]`。
    /// 2. 将剩余后缀 `s[len..]` 反转后添加到原字符串之前。
    ///
    /// # 滚动哈希优化
    /// - 使用多项式哈希（base = 131，模 2^64 自然溢出）快速比较子串。
    /// - 比较正向前缀与反向字符串的对应后缀，O(1) 判断回文。
    ///
    /// # 参数
    /// - `s`: 输入字符串。
    ///
    /// # 返回值
    /// - 最短回文串。
    pub fn shortest_palindrome(s: String) -> String {
        let bytes = s.as_bytes();
        let n = bytes.len();
        if n <= 1 {
            return s;
        }

        const BASE: u64 = 131;               // 哈希基数（经验值，可换为随机大数）
        let mut pow = vec![1u64; n + 1];     // pow[i] = BASE^i
        for i in 1..=n {
            pow[i] = pow[i - 1].wrapping_mul(BASE);
        }

        // 正向哈希：hash[i] = hash(s[0..i])  (i 从 0 到 n)
        let mut forward = vec![0u64; n + 1];
        for i in 0..n {
            let val = (bytes[i] - b'a' + 1) as u64; // 字符映射为 1~26
            forward[i + 1] = forward[i].wrapping_mul(BASE).wrapping_add(val);
        }

        // 反向字符串：rev_bytes = s 的逆序
        let rev_bytes: Vec<u8> = bytes.iter().rev().cloned().collect();
        let mut backward = vec![0u64; n + 1];
        for i in 0..n {
            let val = (rev_bytes[i] - b'a' + 1) as u64;
            backward[i + 1] = backward[i].wrapping_mul(BASE).wrapping_add(val);
        }

        // 获取子串哈希的工具闭包（半开区间 [l, r)）
        let hash_range = |hash_arr: &[u64], l: usize, r: usize| -> u64 {
            // hash_arr[r] - hash_arr[l] * BASE^(r-l)
            hash_arr[r].wrapping_sub(hash_arr[l].wrapping_mul(pow[r - l]))
        };

        // 寻找最长回文前缀的长度
        let max_len = (1..=n)
            .rev()                     // 从长到短尝试，找到即停止（但为了函数式保留全部遍历）
            .filter(|&len| {
                // 正向前缀哈希
                let forward_hash = hash_range(&forward, 0, len);
                // 反向字符串中对应后缀的哈希：rev[ n-len .. n ]
                let rev_hash = hash_range(&backward, n - len, n);
                forward_hash == rev_hash
            })
            .max()                     // 取最大长度（实际因为从大到小，第一个就是最大）
            .unwrap_or(0);

        // 构造结果：反转剩余部分 + 原字符串
        let suffix = &s[max_len..];
        let added: String = suffix.chars().rev().collect();
        added + &s
    }
}
```
