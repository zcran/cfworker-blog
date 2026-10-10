---
title: "leetcode-字符串3"
date: 2026-10-10T12:56:51+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 外观数列

「外观数列」是一个数位字符串序列，由递归公式定义：

countAndSay(1) = "1"
countAndSay(n) 是 countAndSay(n-1) 的行程长度编码。


行程长度编码（RLE）是一种字符串压缩方法，其工作原理是通过将每个最大连续相同字符组替换为该组的长度后加上该字符本身。例如，要压缩字符串 "3322251" ，我们将 "33" 用 "23" 替换，将 "222" 用 "32" 替换，将 "5" 用 "15" 替换并将 "1" 用 "11" 替换。因此压缩后字符串变为 "23321511"。

给定一个整数 n ，返回 外观数列 的第 n 个元素。


```
impl Solution {
    pub fn count_and_say(n: i32) -> String {
        let mut s = String::from("1");

        for _ in 1..n {
            let bytes = s.as_bytes();
            let mut next = String::with_capacity(bytes.len() * 2);
            let mut i = 0;

            // 扫描连续相同字符组，直接以字节形式输出「个数 + 字符」
            while i < bytes.len() {
                let ch = bytes[i];
                let mut j = i + 1;
                while j < bytes.len() && bytes[j] == ch {
                    j += 1;
                }
                // 组长度转十进制字符串推入（长度不会超过 3 位，故用栈式写法足够）
                next.push_str(&(j - i).to_string());
                next.push(ch as char);
                i = j;
            }

            s = next;
        }

        s
    }
}
```
