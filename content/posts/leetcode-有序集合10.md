---
title: "leetcode-有序集合10"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 考场就座

在考场里，有 n 个座位排成一行，编号为 0 到 n - 1。

当学生进入考场后，他必须坐在离最近的人最远的座位上。如果有多个这样的座位，他会坐在编号最小的座位上。(另外，如果考场里没有人，那么学生就坐在 0 号座位上。)

设计一个模拟所述考场的类。

实现 ExamRoom 类：

· ExamRoom(int n) 用座位的数量 n 初始化考场对象。
· int seat() 返回下一个学生将会入座的座位编号。
· void leave(int p) 指定坐在座位 p 的学生将离开教室。保证座位 p 上会有一位学生。


```
use std::collections::BTreeSet;

/// 考场座位分配 - 最大化最近距离
///
/// # 算法思路
/// 使用 BTreeSet 维护已占用的座位，每次选择距离最近的人最远的座位。
///
/// 关键观察：对于任意两个相邻的已占用座位 l 和 r，中间的空座位中，
/// 最优位置是 (l + r) / 2，距离为 (r - l) / 2。
///
/// # 复杂度
/// - seat: O(n)，最坏情况需要遍历所有座位
/// - leave: O(log n)
/// - 空间复杂度: O(n)
///
/// # 示例
/// ```
/// let mut room = ExamRoom::new(10);
/// assert_eq!(room.seat(), 0);
/// assert_eq!(room.seat(), 9);
/// assert_eq!(room.seat(), 4);
/// room.leave(4);
/// assert_eq!(room.seat(), 4);
/// ```
struct ExamRoom {
    n: i32,              // 总座位数
    seats: BTreeSet<i32>, // 已占用的座位
}

impl ExamRoom {
    /// 初始化考场
    pub fn new(n: i32) -> Self {
        Self {
            n,
            seats: BTreeSet::new(),
        }
    }

    /// 学生入座，返回座位编号
    pub fn seat(&mut self) -> i32 {
        // 情况1：考场为空
        if self.seats.is_empty() {
            self.seats.insert(0);
            return 0;
        }

        // 情况2：考场非空，找出最优座位
        let mut best_seat = 0;
        let mut max_dist = 0;

        // 检查第一个座位（与左边界的关系）
        let first = *self.seats.iter().next().unwrap();
        if first > 0 {
            best_seat = 0;
            max_dist = first; // 到最近的人的距离 = first
        }

        // 检查两个已占用座位之间的空位
        let mut prev = first;
        for &curr in self.seats.iter().skip(1) {
            let dist = (curr - prev) / 2; // 到两端最近距离
            if dist > max_dist || (dist == max_dist && prev + dist < best_seat) {
                best_seat = prev + dist;
                max_dist = dist;
            }
            prev = curr;
        }

        // 检查最后一个座位（与右边界的关系）
        let last = *self.seats.iter().rev().next().unwrap();
        let dist_to_end = self.n - 1 - last;
        if dist_to_end > max_dist || (dist_to_end == max_dist && self.n - 1 < best_seat) {
            best_seat = self.n - 1;
            max_dist = dist_to_end;
        }

        // 标记座位已被占用
        self.seats.insert(best_seat);
        best_seat
    }

    /// 学生离开座位
    pub fn leave(&mut self, p: i32) {
        self.seats.remove(&p);
    }
}
```
