---
title: "leetcode-动态规划25"
date: 2026-10-05T20:39:55+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 环绕字符串中唯一的子字符串

定义字符串 base 为一个 "abcdefghijklmnopqrstuvwxyz" 无限环绕的字符串，所以 base 看起来是这样的：

"...zabcdefghijklmnopqrstuvwxyzabcdefghijklmnopqrstuvwxyzabcd....".

给你一个字符串 s ，请你统计并返回 s 中有多少 不同非空子串 也在 base 中出现。



```
impl Solution {
    pub fn find_substring_in_wrapround_string(s: String) -> i32 {
        // dp[c]：以字符 c 结尾、在 base 中出现的最长连续子串长度
        let mut dp = [0i32; 26];
        let bytes = s.as_bytes();
        let mut cur = 0i32;

        for i in 0..bytes.len() {
            // 判断与前一个字符是否在 base 中连续（z -> a 也算连续）
            if i > 0
                && (bytes[i] - bytes[i - 1] == 1 || bytes[i - 1] - bytes[i] == 25)
            {
                cur += 1;
            } else {
                cur = 1;
            }
            // 以该字符结尾的最长长度取较大值
            let idx = (bytes[i] - b'a') as usize;
            dp[idx] = dp[idx].max(cur);
        }

        // 每个字符 c 贡献 dp[c] 个不同的、以 c 结尾的合法子串
        dp.iter().sum()
    }
}
```
