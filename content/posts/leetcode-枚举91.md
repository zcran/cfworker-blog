---
title: "leetcode-枚举91"
date: 2026-07-09T10:05:03+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 范围内总波动值 I

给你两个整数 num1 和 num2，表示一个 闭 区间 [num1, num2]。

一个数字的 波动值 定义为该数字中 峰 和 谷 的总数：

如果一个数位 严格大于 其两个相邻数位，则该数位为 峰。
如果一个数位 严格小于 其两个相邻数位，则该数位为 谷。
数字的第一个和最后一个数位 不能 是峰或谷。

任何少于 3 位的数字，其波动值均为 0。

返回范围 [num1, num2] 内所有数字的波动值之和。


```
impl Solution {
    pub fn total_waviness(num1: i32, num2: i32) -> i32 {
        let mut total = 0;
        for num in num1..=num2 {
            total += Self::waviness_of(num);
        }
        total
    }

    /// 计算单个数字的波动值（峰和谷的总数）
    fn waviness_of(mut num: i32) -> i32 {
        // 少于 3 位的数字波动值为 0
        if num < 100 {
            return 0;
        }

        // 提取所有数位到数组（从低位到高位）
        let mut digits = [0u8; 10];
        let mut len = 0;
        while num > 0 {
            digits[len] = (num % 10) as u8;
            num /= 10;
            len += 1;
        }

        let mut waviness = 0;
        // 检查内部位置：从高位到低位的顺序
        // digits 存储的是低位到高位，所以最高位索引 len-1
        for i in (1..len - 1).rev() {
            let prev = digits[i + 1]; // 更高位
            let curr = digits[i];
            let next = digits[i - 1]; // 更低位
            if (curr > prev && curr > next) || (curr < prev && curr < next) {
                waviness += 1;
            }
        }
        waviness
    }
}
```
