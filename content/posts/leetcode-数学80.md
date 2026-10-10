---
title: "leetcode-数学80"
date: 2026-10-07T20:39:33+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 连接非零数字并乘以其数字和 I

给你一个整数 n。

将 n 中所有的 非零数字 按照它们的原始顺序连接起来，形成一个新的整数 x。如果不存在 非零数字 ，则 x = 0。

sum 为 x 中所有数字的 数字和 。

返回一个整数，表示 x * sum 的值。


```
impl Solution {
    pub fn sum_and_multiply(n: i32) -> i64 {
        // 从左到右遍历 n 的各位数字：
        //   - 累加所有数字到 sum
        //   - 非零数字依次拼接到 x 上
        let mut x = 0i64;
        let mut sum = 0i64;

        for b in n.to_string().bytes() {
            let d = (b - b'0') as i64;
            sum += d;
            if d != 0 {
                x = x * 10 + d;
            }
        }

        x * sum
    }
}
```
