---
title: "leetcode-模拟116"
date: 2026-08-08T11:31:15+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 哈希分割字符串

给你一个长度为 n 的字符串 s 和一个整数 k ，n 是 k 的 倍数 。你的任务是将字符串 s 哈希为一个长度为 n / k 的新字符串 result 。

首先，将 s 分割成 n / k 个 子字符串 ，每个子字符串的长度都为 k 。然后，将 result 初始化为一个 空 字符串。

我们依次从前往后处理每一个 子字符串 ：

一个字符的 哈希值 是它在 字母表 中的下标（也就是 'a' → 0 ，'b' → 1 ，... ，'z' → 25）。
将子字符串中字母的 哈希值 求和。
将和对 26 取余，将结果记为 hashedChar 。
找到小写字母表中 hashedChar 对应的字符。
将该字符添加到 result 的末尾。

返回 result 。


```
impl Solution {
    /// 将字符串 s 按每 k 个字符分块哈希，返回长度为 n/k 的结果字符串
    ///
    /// 哈希规则：每块内字符的字母表下标之和 % 26，映射回对应小写字母
    ///
    /// # 参数
    /// - `s`: 待哈希的字符串，长度 n 是 k 的倍数
    /// - `k`: 每块的长度
    ///
    /// # 返回值
    /// - 哈希后的字符串，长度为 n / k
    pub fn string_hash(s: String, k: i32) -> String {
        let k = k as usize;
        let bytes = s.as_bytes();
        let n = bytes.len();
        let mut result = String::with_capacity(n / k);

        // 按步长 k 遍历字符串，每次处理一个长度为 k 的块
        for chunk in bytes.chunks(k) {
            let sum: usize = chunk.iter().map(|&b| (b - b'a') as usize).sum();
            result.push((b'a' + (sum % 26) as u8) as char);
        }

        result
    }
}
```
