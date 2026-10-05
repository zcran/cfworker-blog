---
title: "leetcode-位运算27"
date: 2026-09-24T10:33:35+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 安排电影院座位

![](../pic/cinema_seats_1.png)

如上图所示，电影院的观影厅中有 n 行座位，行编号从 1 到 n ，且每一行内总共有 10 个座位，列编号从 1 到 10 。

给定一个二维数组 reservedSeats ，其中 reservedSeats[i] = [rowi, seati] 表示第 rowi 行的座位 seati 已经被预定。

四人小组必须被安排在同一排的四个座位上。该小组可以坐在以下座位块之一：

座位 2, 3, 4, 5
座位 4, 5, 6, 7
座位 6, 7, 8, 9
只有当该块中的所有座位都 没有 被预订时，才能使用该块。每个座位 最多 只能分配给一个小组。

返回一个整数，表示可以分配的 最大 四人小组数量。


```
use std::collections::HashMap;

impl Solution {
    /// 计算最多能安排多少个四人小组。
    ///
    /// 每排有 10 个座位，4 人小组只能选以下三种座位块之一：
    /// - 左块  : 座位 2,3,4,5
    /// - 中块  : 座位 4,5,6,7
    /// - 右块  : 座位 6,7,8,9
    ///
    /// 每排最多容纳 2 组。仅当有人预订了 2..=9 中的座位时，
    /// 该排的容量才可能从 2 减少，因此只需记录这些排的被占情况。
    /// 用 8 位掩码表示座位 2..=9 的占用情况（第 i 位对应座位 i+2）。
    pub fn max_number_of_families(n: i32, reserved_seats: Vec<Vec<i32>>) -> i32 {
        // 三种座位块对应的占用掩码（位 0 对应座位 2）
        const LEFT:   i32 = 0b0000_1111; // 座位 2,3,4,5
        const MIDDLE: i32 = 0b0011_1100; // 座位 4,5,6,7
        const RIGHT:  i32 = 0b1111_0000; // 座位 6,7,8,9

        // 仅记录座位 2..=9 被预订的行，其余预订不影响 4 人小组安排
        let mut occupied: HashMap<i32, i32> = HashMap::new();
        for seat in &reserved_seats {
            let col = seat[1];
            if (2..=9).contains(&col) {
                *occupied.entry(seat[0]).or_insert(0) |= 1 << (col - 2);
            }
        }

        // 未被任何相关预订影响的行，每行可安排 2 组
        let mut ans = (n - occupied.len() as i32) * 2;

        // 对被影响的行，检查能否安排 1 组或 2 组
        for &mask in occupied.values() {
            let can_left   = mask & LEFT   == 0;
            let can_middle = mask & MIDDLE == 0;
            let can_right  = mask & RIGHT  == 0;

            // 左右两块可同时使用，安排 2 组
            if can_left && can_right {
                ans += 2;
            } else if can_left || can_middle || can_right {
                // 否则至多安排 1 组
                ans += 1;
            }
        }

        ans
    }
}
```
