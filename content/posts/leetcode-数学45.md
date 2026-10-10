---
title: "leetcode-数学45"
date: 2026-10-07T20:39:31+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 根据规则将箱子分类

给你四个整数 length ，width ，height 和 mass ，分别表示一个箱子的三个维度和质量，请你返回一个表示箱子 类别 的字符串。

· 如果满足以下条件，那么箱子是 "Bulky" 的：
    · 箱子 至少有一个 维度大于等于 104 。
    · 或者箱子的 体积 大于等于 109 。
· 如果箱子的质量大于等于 100 ，那么箱子是 "Heavy" 的。
· 如果箱子同时是 "Bulky" 和 "Heavy" ，那么返回类别为 "Both" 。
· 如果箱子既不是 "Bulky" ，也不是 "Heavy" ，那么返回类别为 "Neither" 。
· 如果箱子是 "Bulky" 但不是 "Heavy" ，那么返回类别为 "Bulky" 。
· 如果箱子是 "Heavy" 但不是 "Bulky" ，那么返回类别为 "Heavy" 。

注意，箱子的体积等于箱子的长度、宽度和高度的乘积。




```
impl Solution {
    pub fn categorize_box(length: i32, width: i32, height: i32, mass: i32) -> String {
        // Bulky：任一维度 >= 10^4，或体积 >= 10^9（用 i64 防溢出）
        let bulky = length >= 10_000
            || width >= 10_000
            || height >= 10_000
            || length as i64 * width as i64 * height as i64 >= 1_000_000_000;

        // Heavy：质量 >= 100
        let heavy = mass >= 100;

        // 按两种情况组合返回对应类别
        match (bulky, heavy) {
            (true, true) => "Both",
            (true, false) => "Bulky",
            (false, true) => "Heavy",
            (false, false) => "Neither",
        }
        .to_string()
    }
}
```
