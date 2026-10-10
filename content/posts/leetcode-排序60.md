---
title: "leetcode-排序60"
date: 2026-10-05T00:35:03+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 将字符串中的元音字母排序

给你一个下标从 0 开始的字符串 s ，将 s 中的元素重新 排列 得到新的字符串 t ，它满足：

所有辅音字母都在原来的位置上。更正式的，如果满足 0 <= i < s.length 的下标 i 处的 s[i] 是个辅音字母，那么 t[i] = s[i] 。

元音字母都必须以他们的 ASCII 值按 非递减 顺序排列。更正式的，对于满足 0 <= i < j < s.length 的下标 i 和 j  ，如果 s[i] 和 s[j] 都是元音字母，那么 t[i] 的 ASCII 值不能大于 t[j] 的 ASCII 值。

请你返回结果字母串。

元音字母为 'a' ，'e' ，'i' ，'o' 和 'u' ，它们可能是小写字母也可能是大写字母，辅音字母是除了这 5 个字母以外的所有字母。


```
impl Solution {
    /// 将 s 中的元音字母按 ASCII 值非递减排列，辅音字母保持原位。
    ///
    /// 思路：
    /// 1. 提取所有元音字母并排序。
    /// 2. 遍历原字符串，遇到元音时依次填入排序后的元音，辅音保持不变。
    pub fn sort_vowels(s: String) -> String {
        // 判断字符是否为元音（含大小写）
        fn is_vowel(c: char) -> bool {
            matches!(c, 'a' | 'e' | 'i' | 'o' | 'u' | 'A' | 'E' | 'I' | 'O' | 'U')
        }

        // 收集并排序所有元音
        let mut vowels: Vec<char> = s.chars().filter(|&c| is_vowel(c)).collect();
        vowels.sort_unstable();

        // 按原位置回填：元音位置用排序后的元音替换
        let mut it = vowels.into_iter();
        s.chars()
            .map(|c| if is_vowel(c) { it.next().unwrap() } else { c })
            .collect()
    }
}
```
