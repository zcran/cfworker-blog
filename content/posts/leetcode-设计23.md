---
title: "leetcode-设计23"
date: 2026-07-14T10:41:25+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 座位预约管理系统

请你设计一个管理 n 个座位预约的系统，座位编号从 1 到 n 。

请你实现 SeatManager 类：

SeatManager(int n) 初始化一个 SeatManager 对象，它管理从 1 到 n 编号的 n 个座位。所有座位初始都是可预约的。

int reserve() 返回可以预约座位的 最小编号 ，此座位变为不可预约。

void unreserve(int seatNumber) 将给定编号 seatNumber 对应的座位变成可以预约。


```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

/// 座位预约管理系统
///
/// 使用最小堆维护可预约座位，始终返回最小编号
/// 时间复杂度: reserve O(log n), unreserve O(log n)
/// 空间复杂度: O(n)
struct SeatManager {
    heap: BinaryHeap<Reverse<i32>>, // 最小堆，堆顶为最小编号
}

impl SeatManager {
    /// 初始化 n 个座位，全部可预约
    fn new(n: i32) -> Self {
        // 使用 from_iter 批量构建，比循环 push 更高效
        let heap = (1..=n).map(Reverse).collect();
        SeatManager { heap }
    }

    /// 预约最小编号的可用座位
    fn reserve(&mut self) -> i32 {
        self.heap.pop().unwrap().0
    }

    /// 释放指定座位，使其可重新预约
    fn unreserve(&mut self, seat_number: i32) {
        self.heap.push(Reverse(seat_number));
    }
}
```
