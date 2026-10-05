---
title: "leetcode-位运算26"
date: 2026-09-24T10:33:35+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 将数字变成 0 的操作次数

给你一个非负整数 num ，请你返回将它变成 0 所需要的步数。 如果当前数字是偶数，你需要把它除以 2 ；否则，减去 1 。


```
impl Solution {
    /// 返回将 num 变为 0 所需的步数。
    ///
    /// 规则：偶数除以 2，奇数减 1。
    /// 等价于每次消除最低位：偶数右移一位，奇数清掉最低的 1。
    pub fn number_of_steps(mut num: i32) -> i32 {
        let mut steps = 0;
        while num != 0 {
            // 奇数减 1（清最低位），偶数除以 2（右移一位）
            num = if num & 1 == 1 { num - 1 } else { num >> 1 };
            steps += 1;
        }
        steps
    }
}
```
