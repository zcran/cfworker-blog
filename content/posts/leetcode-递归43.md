---
title: "leetcode-递归43"
date: 2026-05-26T10:00:18+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 数字 1 的个数

给定一个整数 num，计算所有小于等于 num 的非负整数中数字 1 出现的个数。


```
impl Solution {
    pub fn digit_one_in_number(n: i32) -> i32 {
        if n <= 0 {
            return 0;
        }

        let mut count = 0;
        let mut factor = 1;  // 当前位权值：1, 10, 100, ...
        let mut num = n;

        while num > 0 {
            let digit = num % 10;           // 当前位数字
            let higher = num / 10;          // 高位部分
            let lower = n % factor;         // 低位部分

            // 计算当前位出现1的次数
            count += higher * factor;        // 高位贡献

            if digit == 1 {
                count += lower + 1;          // 当前位为1，加上低位+1
            } else if digit > 1 {
                count += factor;              // 当前位大于1，加上完整权值
            }

            // 移动到下一位
            factor *= 10;
            num = higher;
        }

        count
    }
}
```
