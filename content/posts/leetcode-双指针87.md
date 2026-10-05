---
title: "leetcode-双指针87"
date: 2026-09-05T09:53:06+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 坐上公交的最晚时间

给你一个下标从 0 开始长度为 n 的整数数组 buses ，其中 buses[i] 表示第 i 辆公交车的出发时间。同时给你一个下标从 0 开始长度为 m 的整数数组 passengers ，其中 passengers[j] 表示第 j 位乘客的到达时间。所有公交车出发的时间互不相同，所有乘客到达的时间也互不相同。

给你一个整数 capacity ，表示每辆公交车 最多 能容纳的乘客数目。

每位乘客都会排队搭乘下一辆有座位的公交车。如果你在 y 时刻到达，公交在 x 时刻出发，满足 y <= x  且公交没有满，那么你可以搭乘这一辆公交。最早 到达的乘客优先上车。

返回你可以搭乘公交车的最晚到达公交站时间。你 不能 跟别的乘客同时刻到达。

注意：数组 buses 和 passengers 不一定是有序的。


```
use std::collections::HashSet;

impl Solution {
    pub fn latest_time_catch_the_bus(mut buses: Vec<i32>, mut passengers: Vec<i32>, capacity: i32) -> i32 {
        buses.sort_unstable();
        passengers.sort_unstable();

        // 记录所有乘客到达时间，用于快速判断某个时刻是否已被占用
        let passenger_set: HashSet<i32> = passengers.iter().copied().collect();

        let mut pos = 0;          // 下一个待上车乘客的下标
        let mut last_bus = 0;     // 最后一辆公交车的出发时间
        let mut is_full = false;  // 最后一辆公交车是否已满

        // 模拟每辆公交车按顺序接载乘客
        for &bus in &buses {
            last_bus = bus;
            let mut space = capacity;
            while space > 0 && pos < passengers.len() && passengers[pos] <= bus {
                space -= 1;
                pos += 1;
            }
            is_full = space == 0;
        }

        // 确定最晚到达时间的起始候选值
        let mut candidate = if is_full {
            // 最后一辆公交车已满，必须早于最后一个上车的乘客
            passengers[pos - 1] - 1
        } else {
            // 最后一辆公交车还有空位，可以尝试其出发时间
            last_bus
        };

        // 向前寻找第一个未被乘客占用的时刻
        while passenger_set.contains(&candidate) {
            candidate -= 1;
        }

        candidate
    }
}
```
