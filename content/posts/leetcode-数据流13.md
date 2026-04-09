---
title: "leetcode-数据流13"
date: 2026-04-06T20:57:54+08:00
tags: ["leetcode", "数据流"]
draft: false
---


## 找到数据流中的连续整数

给你一个整数数据流，请你实现一个数据结构，检查数据流中最后 k 个整数是否 等于 给定值 value 。

请你实现 DataStream 类：

DataStream(int value, int k) 用两个整数 value 和 k 初始化一个空的整数数据流。
boolean consec(int num) 将 num 添加到整数数据流。如果后 k 个整数都等于 value ，返回 true ，否则返回 false 。如果少于 k 个整数，条件不满足，所以也返回 false 。


```
struct DataStream {
    value: i32,
    k: i32,
    count: i32,
}

impl DataStream {
    fn new(value: i32, k: i32) -> Self {
        Self { value, k, count: 0 }
    }

    fn consec(&mut self, num: i32) -> bool {
        self.count = if num == self.value { self.count + 1 } else { 0 };
        self.count >= self.k
    }
}
```
