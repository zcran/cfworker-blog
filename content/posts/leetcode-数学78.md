---
title: "leetcode-数学78"
date: 2026-10-07T20:39:33+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 计算十进制表示

给你一个 正整数 n。

如果一个正整数可以表示为 1 到 9 的单个数字和 10 的非负整数次幂的乘积，则称这个整数是一个 10 进制分量。例如，500、30 和 7 是 10 进制分量 ，而 537、102 和 11 则不是。

请将 n 表示为若干 仅由 10 进制分量组成的和，且使用的 10 进制分量个数 最少 。

返回一个包含这些 10 进制分量 的数组，并按分量大小 降序 排列。


```
impl Solution {
    pub fn decimal_representation(mut n: i32) -> Vec<i32> {
        // 每位非零数字 d 对应一个 10 进制分量 d * 10^pos，
        // 从低位到高位生成，最后反转即为降序。
        let mut ans = Vec::with_capacity(10);

        let mut base = 1;
        while n > 0 {
            let d = n % 10;
            if d != 0 {
                ans.push(d * base);
            }
            n /= 10;
            base *= 10;
        }

        ans.reverse();
        ans
    }
}
```
