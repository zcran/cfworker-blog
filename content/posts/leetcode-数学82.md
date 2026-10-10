---
title: "leetcode-数学82"
date: 2026-10-07T20:39:34+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 整数的镜像距离

给你一个整数 n。

定义它的 镜像距离 为：abs(n - reverse(n))，其中 reverse(n) 表示将 n 的数字反转后形成的整数。

返回表示 n 的镜像距离的整数。

其中，abs(x) 表示 x 的绝对值。




```
impl Solution {
    pub fn mirror_distance(n: i32) -> i32 {
        // 反转 n 的数字（忽略前导零），再求与原数之差的绝对值
        let mut x = n;
        let mut reversed = 0;
        while x > 0 {
            reversed = reversed * 10 + x % 10;
            x /= 10;
        }

        (n - reversed).abs()
    }
}
```
