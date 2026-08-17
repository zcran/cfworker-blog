---
title: "leetcode-模拟46"
date: 2026-08-08T11:31:11+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 增长的内存泄露

给你两个整数 memory1 和 memory2 分别表示两个内存条剩余可用内存的位数。现在有一个程序每秒递增的速度消耗着内存。

在第 i 秒（秒数从 1 开始），有 i 位内存被分配到 剩余内存较多 的内存条（如果两者一样多，则分配到第一个内存条）。如果两者剩余内存都不足 i 位，那么程序将 意外退出 。

请你返回一个数组，包含 [crashTime, memory1crash, memory2crash] ，其中 crashTime是程序意外退出的时间（单位为秒）， memory1crash 和 memory2crash 分别是两个内存条最后剩余内存的位数。


```
impl Solution {
   pub fn mem_leak(mut memory1: i32, mut memory2: i32) -> Vec<i32> {
       let mut i = 1;
       while i <= memory1.max(memory2) {
           if memory1 >= memory2 {
               memory1 -= i;
           } else {
               memory2 -= i;
           }
           i += 1;
       }
       vec![i, memory1, memory2]
   }
}
```
