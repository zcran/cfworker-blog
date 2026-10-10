---
title: "leetcode-字符串50"
date: 2026-10-10T12:56:55+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 字符串中最大的 3 位相同数字

给你一个字符串 num ，表示一个大整数。如果一个整数满足下述所有条件，则认为该整数是一个 优质整数 ：

· 该整数是 num 的一个长度为 3 的 子字符串 。
· 该整数由唯一一个数字重复 3 次组成。

以字符串形式返回 最大的优质整数 。如果不存在满足要求的整数，则返回一个空字符串 "" 。

注意：

· 子字符串 是字符串中的一个连续字符序列。
· num 或优质整数中可能存在 前导零 。


```
impl Solution {
    pub fn largest_good_integer(num: String) -> String {
        let b = num.as_bytes();
        let mut best = 0u8;

        // 滑动窗口检查连续三个相同数字，记录最大值
        for w in b.windows(3) {
            if w[0] == w[1] && w[1] == w[2] && w[0] > best {
                best = w[0];
            }
        }

        if best == 0 {
            String::new()
        } else {
            // 构造三个相同数字组成的字符串
            let c = best as char;
            format!("{c}{c}{c}")
        }
    }
}
```
