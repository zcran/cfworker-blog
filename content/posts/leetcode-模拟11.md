---
title: "leetcode-模拟11"
date: 2026-08-08T11:31:08+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 复数乘法

复数 可以用字符串表示，遵循 "实部+虚部i" 的形式，并满足下述条件：

实部 是一个整数，取值范围是 [-100, 100]

虚部 也是一个整数，取值范围是 [-100, 100]

i2 == -1

给你两个字符串表示的复数 num1 和 num2 ，请你遵循复数表示形式，返回表示它们乘积的字符串。


```
impl Solution {
    pub fn complex_number_multiply(num1: String, num2: String) -> String {
        let (a, b) = Self::parse(&num1);
        let (c, d) = Self::parse(&num2);
        // (a+bi)(c+di) = (ac-bd) + (ad+bc)i
        format!("{}+{}i", a * c - b * d, a * d + b * c)
    }

    // 解析 "实部+虚部i" 为 (实部, 虚部)
    fn parse(s: &str) -> (i32, i32) {
        let plus = s.find('+').unwrap();
        let re = s[..plus].parse::<i32>().unwrap();
        let im = s[plus + 1..s.len() - 1].parse::<i32>().unwrap();
        (re, im)
    }
}
```
