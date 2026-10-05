---
title: "leetcode-位运算98"
date: 2026-09-24T10:33:40+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 配对交换

配对交换。编写程序，交换某个整数的奇数位和偶数位，尽量使用较少的指令（也就是说，位 0 与位 1 交换，位 2 与位 3 交换，以此类推）。

```
impl Solution {
    pub fn exchange_bits(num: i32) -> i32 {
        // 用掩码一次性交换所有奇偶位：
        //   偶位（第 0,2,4,... 位）掩码 0x55555555，左移 1 位到奇位
        //   奇位（第 1,3,5,... 位）掩码 0xAAAAAAAA，右移 1 位到偶位
        // 两部分的并集即为交换后的结果。
        let even = num & 0x5555_5555; // 偶位（低位起第 0 位）
        let odd  = num & 0xAAAA_AAAA_u32 as i32; // 奇位
        (even << 1) | ((odd as u32 >> 1) as i32)
    }
}
```
