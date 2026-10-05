---
title: "leetcode-贪心127"
date: 2026-10-03T12:55:42+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 最大二进制奇数

给你一个 二进制 字符串 s ，其中至少包含一个 '1' 。

你必须按某种方式 重新排列 字符串中的位，使得到的二进制数字是可以由该组合生成的 最大二进制奇数 。

以字符串形式，表示并返回可以由给定组合生成的最大二进制奇数。

注意 返回的结果字符串 可以 含前导零。


```
impl Solution {
    pub fn maximum_odd_binary_number(s: String) -> String {
        // 最大二进制奇数：末位必须为 '1'，其余 '1' 尽量靠前，'0' 全部放中间。
        let ones = s.bytes().filter(|&b| b == b'1').count();
        let zeros = s.len() - ones;

        let mut res = String::with_capacity(s.len());
        res.push_str(&"1".repeat(ones - 1)); // 除末位外所有 '1' 放最前
        res.push_str(&"0".repeat(zeros));    // 所有 '0' 居中
        res.push('1');                       // 末位固定为 '1' 保证奇数
        res
    }
}
```
