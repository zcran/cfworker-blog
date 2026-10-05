---
title: "leetcode-贪心81"
date: 2026-10-03T12:55:39+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 子字符串突变后可能得到的最大整数

给你一个字符串 num ，该字符串表示一个大整数。另给你一个长度为 10 且 下标从 0  开始 的整数数组 change ，该数组将 0-9 中的每个数字映射到另一个数字。更规范的说法是，数字 d 映射为数字 change[d] 。

你可以选择 突变  num 的任一子字符串。突变 子字符串意味着将每位数字 num[i] 替换为该数字在 change 中的映射（也就是说，将 num[i] 替换为 change[num[i]]）。

请你找出在对 num 的任一子字符串执行突变操作（也可以不执行）后，可能得到的 最大整数 ，并用字符串表示返回。

子字符串 是字符串中的一个连续序列。


```
impl Solution {
    pub fn maximum_number(num: String, change: Vec<i32>) -> String {
        // 状态：0 = 尚未开始突变，1 = 正在突变，2 = 突变已结束
        let mut state = 0u8;
        let mut result = String::with_capacity(num.len());

        for c in num.bytes() {
            let d = (c - b'0') as usize;
            let mapped = change[d] as u8 + b'0';
            match state {
                // 未开始：映射更大则开始突变，否则保持原字符
                0 => {
                    if mapped > c {
                        state = 1;
                        result.push(mapped as char);
                    } else {
                        result.push(c as char);
                    }
                }
                // 突变中：映射不小于当前则继续突变，否则结束突变
                1 => {
                    if mapped >= c {
                        result.push(mapped as char);
                    } else {
                        state = 2;
                        result.push(c as char);
                    }
                }
                // 已结束：保持原字符
                _ => result.push(c as char),
            }
        }
        result
    }
}
```
