---
title: "leetcode-贪心87"
date: 2026-10-03T12:55:39+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 使每位学生都有座位的最少移动次数

一个房间里有 n 个 空闲 座位和 n 名 站着的 学生，房间用一个数轴表示。给你一个长度为 n 的数组 seats ，其中 seats[i] 是第 i 个座位的位置。同时给你一个长度为 n 的数组 students ，其中 students[j] 是第 j 位学生的位置。

你可以执行以下操作任意次：

增加或者减少第 i 位学生的位置，每次变化量为 1 （也就是将第 i 位学生从位置 x 移动到 x + 1 或者 x - 1）
请你返回使所有学生都有座位坐的 最少移动次数 ，并确保没有两位学生的座位相同。

请注意，初始时有可能有多个座位或者多位学生在 同一 位置。


```
impl Solution {
    pub fn min_moves_to_seat(mut seats: Vec<i32>, mut students: Vec<i32>) -> i32 {
        // 排序后按位配对，同序对应可使总移动距离最小
        seats.sort_unstable();
        students.sort_unstable();

        seats
            .iter()
            .zip(&students)
            .map(|(&s, &t)| (s - t).abs())
            .sum()
    }
}
```
