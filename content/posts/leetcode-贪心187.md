---
title: "leetcode-贪心187"
date: 2026-10-03T12:55:46+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 破解闯关密码

闯关游戏需要破解一组密码，闯关组给出的有关密码的线索是：

一个拥有密码所有元素的非负整数数组 password

密码是 password 中所有元素拼接后得到的最小的一个数

请编写一个程序返回这个密码。


```
impl Solution {
    pub fn crack_password(mut nums: Vec<i32>) -> String {
        // 贪心：排序规则为 a 应排在 b 前当且仅当 "ab" < "ba"。
        // 这样拼接后得到的字符串字典序最小，即数值最小。
        nums.sort_unstable_by(|a, b| {
            let ab = format!("{}{}", a, b);
            let ba = format!("{}{}", b, a);
            ab.cmp(&ba)
        });

        nums.iter().map(|x| x.to_string()).collect()
    }
}
```
