---
title: "leetcode-贪心5"
date: 2026-10-03T12:55:34+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 加油站

在一条环路上有 n 个加油站，其中第 i 个加油站有汽油 gas[i] 升。

你有一辆油箱容量无限的的汽车，从第 i 个加油站开往第 i+1 个加油站需要消耗汽油 cost[i] 升。你从其中的一个加油站出发，开始时油箱为空。

给定两个整数数组 gas 和 cost ，如果你可以按顺序绕环路行驶一周，则返回出发时加油站的编号，否则返回 -1 。如果存在解，则 保证 它是 唯一 的。


```
impl Solution {
    pub fn can_complete_circuit(gas: Vec<i32>, cost: Vec<i32>) -> i32 {
        // total: 总油量 - 总消耗，< 0 则无解
        // tank:  从当前起点出发的累计剩余油量，< 0 说明起点到 i 之间都不能作为起点
        let (mut total, mut tank, mut start) = (0, 0, 0);

        for i in 0..gas.len() {
            let diff = gas[i] - cost[i];
            total += diff;
            tank += diff;

            // 当前起点无法到达 i+1，改从 i+1 重新出发
            if tank < 0 {
                start = i + 1;
                tank = 0;
            }
        }

        if total < 0 { -1 } else { start as i32 }
    }
}
```
