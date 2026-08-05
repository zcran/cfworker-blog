---
title: "leetcode-滑动窗口55"
date: 2026-07-18T11:02:32+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 找到一个数字的 K 美丽值

一个整数 num 的 k 美丽值定义为 num 中符合以下条件的 子字符串 数目：

子字符串长度为 k 。
子字符串能整除 num 。

给你整数 num 和 k ，请你返回 num 的 k 美丽值。

注意：

允许有 前缀 0 。
0 不能整除任何值。

一个 子字符串 是一个字符串里的连续一段字符序列。


```
impl Solution {
    pub fn divisor_substrings(num: i32, k: i32) -> i32 {
        let s = num.to_string();
        let k = k as usize;
        let mut count = 0;

        // 遍历所有长度为k的连续子串
        for i in 0..=s.len() - k {
            // 解析子串为整数（允许前导零）
            if let Ok(sub_num) = s[i..i + k].parse::<i32>() {
                // 检查是否能整除原数（除数不能为0）
                if sub_num != 0 && num % sub_num == 0 {
                    count += 1;
                }
            }
        }

        count
    }
}
```
