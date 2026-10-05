---
title: "leetcode-贪心7"
date: 2026-10-03T12:55:34+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 最大数

给定一组非负整数 nums，重新排列每个数的顺序（每个数不可拆分）使之组成一个最大的整数。

注意：输出结果可能非常大，所以你需要返回一个字符串而不是整数。


```
impl Solution {
    pub fn largest_number(mut nums: Vec<i32>) -> String {
        // 按 "a+b" 与 "b+a" 的字典序降序排序，使得拼接结果最大
        nums.sort_unstable_by(|a, b| {
            let (a, b) = (a.to_string(), b.to_string());
            format!("{b}{a}").cmp(&format!("{a}{b}"))
        });

        // 全为 0 时只返回一个 "0"
        if nums[0] == 0 {
            return "0".to_string();
        }

        nums.iter().map(|n| n.to_string()).collect()
    }
}
```
