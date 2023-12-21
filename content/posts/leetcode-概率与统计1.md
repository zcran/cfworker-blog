---
title: "Leetcode 概率与统计1"
date: 2023-12-19T15:47:54+08:00
tags: ["leetcode", "概率与统计"]
draft: false
---

## 飞机座位分配概率

有 n 位乘客即将登机，飞机正好有 n 个座位。第一位乘客的票丢了，他随便选了一个座位坐下。

剩下的乘客将会：

如果他们自己的座位还空着，就坐到自己的座位上，

当他们自己的座位被占用时，随机选择其他座位
第 n 位乘客坐在自己的座位上的概率是多少？


```
impl Solution {
    pub fn nth_person_gets_nth_seat(n: i32) -> f64 {
        if n <= 2 {
            return 1.0 / n as f64
        }
        let mut prob = 0.5;
        for i in 3..=n {
            prob = (1.0 + (i - 2) as f64 * prob) / i  as f64;
        }
        prob
    }
}
```