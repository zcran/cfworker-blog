---
title: "leetcode-数学44"
date: 2026-10-07T20:39:31+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 统计能整除数字的位数

给你一个整数 num ，返回 num 中能整除 num 的数位的数目。

如果满足 nums % val == 0 ，则认为整数 val 可以整除 nums 。


```
impl Solution {
    pub fn count_digits(num: i32) -> i32 {
        // 逐位取出 num 的每个数位 d，若 num % d == 0 则计数。
        // 题目保证 num 的数位不含 0，因此无需处理除零。
        let mut count = 0;
        let mut x = num;
        while x > 0 {
            if num % (x % 10) == 0 {
                count += 1;
            }
            x /= 10;
        }
        count
    }
}
```


```
impl Solution {
    pub fn count_digits(num: i32) -> i32 {
        num.to_string()
            .bytes()
            .filter(|&b| num % (b - b'0') as i32 == 0)
            .count() as i32
    }
}
```
