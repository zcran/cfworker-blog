---
title: "leetcode-数学88"
date: 2026-10-07T20:39:34+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 统计数字出现总次数

给你一个整数数组 nums 和一个整数 digit。

返回在 nums 所有元素的十进制表示中 digit 出现的总次数。


```
impl Solution {
    pub fn count_digit_occurrences(nums: Vec<i32>, digit: i32) -> i32 {
        // 统计每个数中 digit 出现的次数并累加
        let target = b'0' + digit as u8;
        nums.iter()
            .map(|x| x.to_string().bytes().filter(|&b| b == target).count() as i32)
            .sum()
    }
}
```
