---
title: "leetcode-双指针21"
date: 2026-09-05T09:53:02+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 反转字符串中的元音字母

给你一个字符串 s ，仅反转字符串中的所有元音字母，并返回结果字符串。

元音字母包括 'a'、'e'、'i'、'o'、'u'，且可能以大小写两种形式出现不止一次。


```
impl Solution {
    pub fn reverse_vowels(mut s: String) -> String {
        // 将字符串转为可变字节数组
        let bytes = unsafe { s.as_bytes_mut() };
        let mut left = 0;
        let mut right = bytes.len().saturating_sub(1);

        while left < right {
            // 从左找到元音
            while left < right && !is_vowel(bytes[left]) {
                left += 1;
            }
            // 从右找到元音
            while left < right && !is_vowel(bytes[right]) {
                right -= 1;
            }
            if left < right {
                // 交换两个元音
                bytes.swap(left, right);
                left += 1;
                right -= 1;
            }
        }

        s
    }
}

#[inline(always)]
fn is_vowel(b: u8) -> bool {
    matches!(b, b'a' | b'e' | b'i' | b'o' | b'u' | b'A' | b'E' | b'I' | b'O' | b'U')
}
```
