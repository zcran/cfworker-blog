---
title: "leetcode-组合数学14"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 所有子字符串中的元音


给你一个字符串 word ，返回 word 的所有子字符串中 元音的总数 ，元音是指 'a'、'e'、'i'、'o' 和 'u' 。

子字符串 是字符串中一个连续（非空）的字符序列。

注意：由于对 word 长度的限制比较宽松，答案可能超过有符号 32 位整数的范围。计算时需当心。


```
impl Solution {
    pub fn count_vowels(word: String) -> i64 {
        let n = word.len();
        let bytes = word.as_bytes(); // 直接操作字节，避免 UTF-8 解码开销

        let mut ans = 0i64;
        for (i, &ch) in bytes.iter().enumerate() {
            // 使用 matches! 宏快速判断是否为元音（比 find 更快）
            if matches!(ch, b'a' | b'e' | b'i' | b'o' | b'u') {
                // 包含当前字符的子串数量 = (左边可选数) * (右边可选数)
                // 左边有 i 个字符，可选位置共 i+1 个；
                // 右边有 n-i-1 个字符，可选位置共 n-i 个。
                ans += ((n - i) * (i + 1)) as i64;
            }
        }
        ans
    }
}
```
