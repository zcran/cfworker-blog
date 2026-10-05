---
title: "leetcode-贪心31"
date: 2026-10-03T12:55:36+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 坏了的计算器

在显示着数字 startValue 的坏计算器上，我们可以执行以下两种操作：

双倍（Double）：将显示屏上的数字乘 2；
递减（Decrement）：将显示屏上的数字减 1 。

给定两个整数 startValue 和 target 。返回显示数字 target 所需的最小操作数。


```
impl Solution {
    pub fn broken_calc(start_value: i32, mut target: i32) -> i32 {
        // 逆向思考：从 target 回到 startValue
        //   偶数 -> 除以 2（对应正向的双倍）
        //   奇数 -> 加 1 变成偶数再除以 2（对应正向的先减 1 再双倍）
        // 当 target <= startValue 时，只能靠递减逐步逼近
        let mut ops = 0;

        while target > start_value {
            if target % 2 == 0 {
                target /= 2;
            } else {
                target += 1;
            }
            ops += 1;
        }

        ops + start_value - target
    }
}
```
