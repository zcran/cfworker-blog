---
title: "leetcode-双指针25"
date: 2026-09-05T09:53:02+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 压缩字符串

给你一个字符数组 chars ，请使用下述算法压缩：

从一个空字符串 s 开始。对于 chars 中的每组 连续重复字符 ：

· 如果这一组长度为 1 ，则将字符追加到 s 中。

· 否则，需要向 s 追加字符，后跟这一组的长度。

压缩后得到的字符串 s 不应该直接返回 ，需要转储到字符数组 chars 中。需要注意的是，如果组长度为 10 或 10 以上，则在 chars 数组中会被拆分为多个字符。

请在 修改完输入数组后 ，返回该数组的新长度。

你必须设计并实现一个只使用常量额外空间的算法来解决此问题。

注意：数组中超出返回长度的字符无关紧要，应予忽略。


```
impl Solution {
    pub fn compress(chars: &mut Vec<char>) -> i32 {
        let mut write = 0; // 写入位置
        let mut read = 0; // 读取位置

        while read < chars.len() {
            let current_char = chars[read];
            let mut count = 0;

            // 统计连续相同字符的数量
            while read < chars.len() && chars[read] == current_char {
                count += 1;
                read += 1;
            }

            // 写入字符
            chars[write] = current_char;
            write += 1;

            // 如果数量大于1，写入数字
            if count > 1 {
                for digit in count.to_string().chars() {
                    chars[write] = digit;
                    write += 1;
                }
            }
        }

        write as i32
    }
}
```
