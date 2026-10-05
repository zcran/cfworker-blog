---
title: "leetcode-双指针122"
date: 2026-09-05T09:53:08+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 最少翻转次数得到反转二进制字符串

给你一个 正 整数 n。

令 s 为 n 的 二进制表示（不含前导零）。

二进制字符串 s 的 反转 是指将 s 中的字符按相反顺序排列得到的字符串。

你可以翻转 s 中的任意一位（将 0 → 1 或 1 → 0）。每次翻转 仅 影响一位。

请返回使 s 等于其原始形式的反转所需的 最少 翻转次数。


```
impl Solution {
    pub fn minimum_flips(n: i32) -> i32 {
        // 二进制表示中最高位一定是 1，长度即位数。
        let bits = 32 - n.leading_zeros();
        let mut ans = 0;

        // 只需比较对称位置：若 s[i] != s[m-1-i]，则需要两次翻转
        // （把其中一个 0 变 1，另一个 1 变 0，或反之）。
        for i in 0..bits / 2 {
            let left = (n >> (bits - 1 - i)) & 1;
            let right = (n >> i) & 1;
            if left != right {
                ans += 2;
            }
        }

        ans
    }
}
```
