---
title: "leetcode-前缀和15"
date: 2026-08-25T10:02:47+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 航班预订统计

这里有 n 个航班，它们分别从 1 到 n 进行编号。

有一份航班预订表 bookings ，表中第 i 条预订记录 bookings[i] = [firsti, lasti, seatsi] 意味着在从 firsti 到 lasti （包含 firsti 和 lasti ）的 每个航班 上预订了 seatsi 个座位。

请你返回一个长度为 n 的数组 answer，里面的元素是每个航班预定的座位总数。


```
impl Solution {
    pub fn corp_flight_bookings(bookings: Vec<Vec<i32>>, n: i32) -> Vec<i32> {
        let n = n as usize;
        let mut diff = vec![0; n + 1]; // 差分数组，多一个位置用于边界处理

        // 构建差分数组：区间 [first, last] 加 seats
        for booking in bookings {
            let first = booking[0] as usize - 1;
            let last = booking[1] as usize; // 注意：last 是 inclusive，差分结束位置为 last+1
            let seats = booking[2];
            diff[first] += seats;
            diff[last] -= seats; // 这里 last 作为结束位置（因为 diff 长度为 n+1）
        }

        // 前缀和还原出每个航班的座位数
        let mut answer = vec![0; n];
        let mut cur = 0;
        for i in 0..n {
            cur += diff[i];
            answer[i] = cur;
        }

        answer
    }
}
```
