---
title: "leetcode-枚举36"
date: 2026-07-09T10:05:01+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 反转之后的数字和

给你一个 非负 整数 num 。如果存在某个 非负 整数 k 满足 k + reverse(k) = num  ，则返回 true ；否则，返回 false 。

reverse(k) 表示 k 反转每个数位后得到的数字。


```
impl Solution {
    pub fn sum_of_number_and_reverse(num: i32) -> bool {
        // 枚举所有可能的 k，从 0 到 num
        for k in 0..=num {
            let mut x = k;
            let mut rev = 0;
            // 计算 reverse(k)
            while x > 0 {
                rev = rev * 10 + x % 10;
                x /= 10;
            }
            // 检查条件
            if k + rev == num {
                return true;
            }
        }
        false
    }
}
```
