---
title: "leetcode-字符串61"
date: 2026-10-10T12:56:56+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 老人的数目

给你一个下标从 0 开始的字符串数组 details 。details 中每个元素都是一位乘客的信息，信息用长度为 15 的字符串表示，表示方式如下：

前十个字符是乘客的手机号码。
接下来的一个字符是乘客的性别。
接下来两个字符是乘客的年龄。
最后两个字符是乘客的座位号。

请你返回乘客中年龄 严格大于 60 岁 的人数。


```
impl Solution {
    pub fn count_seniors(details: Vec<String>) -> i32 {
        details
            .iter()
            .filter(|s| {
                // 年龄位于第 11、12 位（0-based），只可能是两位数
                let b = s.as_bytes();
                (b[11] - b'0') * 10 + (b[12] - b'0') > 60
            })
            .count() as i32
    }
}
```
