---
title: "leetcode-哈希表51"
date: 2026-10-09T20:07:58+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 统计字符串中的元音子字符串

子字符串 是字符串中的一个连续（非空）的字符序列。

元音子字符串 是 仅 由元音（'a'、'e'、'i'、'o' 和 'u'）组成的一个子字符串，且必须包含 全部五种 元音。

给你一个字符串 word ，统计并返回 word 中 元音子字符串的数目 。


```
impl Solution {
    pub fn count_vowel_substrings(word: String) -> i32 {
        let bytes = word.as_bytes();
        let n = bytes.len();
        let mut ans = 0;

        // 枚举所有可能的起点
        for i in 0..n {
            // 记录当前窗口内各元音出现的次数
            let mut counts = [0u32; 5];
            let mut distinct = 0; // 当前窗口内出现的不同元音种类数

            // 从 i 开始向右扩展窗口
            for j in i..n {
                let idx = match bytes[j] {
                    b'a' => 0,
                    b'e' => 1,
                    b'i' => 2,
                    b'o' => 3,
                    b'u' => 4,
                    _ => break, // 遇到非元音，后续窗口都不合法，直接终止
                };

                // 首次出现该元音时，种类数 +1
                if counts[idx] == 0 {
                    distinct += 1;
                }
                counts[idx] += 1;

                // 若五种元音齐全，则 [i, j] 是一个合法元音子字符串
                if distinct == 5 {
                    ans += 1;
                }
            }
        }

        ans
    }
}
```
