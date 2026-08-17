---
title: "leetcode-模拟94"
date: 2026-08-08T11:31:14+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 总行驶距离

卡车有两个油箱。给你两个整数，mainTank 表示主油箱中的燃料（以升为单位），additionalTank 表示副油箱中的燃料（以升为单位）。

该卡车每耗费 1 升燃料都可以行驶 10 km。每当主油箱使用了 5 升燃料时，如果副油箱至少有 1 升燃料，则会将 1 升燃料从副油箱转移到主油箱。

返回卡车可以行驶的最大距离。

注意：从副油箱向主油箱注入燃料不是连续行为。这一事件会在每消耗 5 升燃料时突然且立即发生。


```
impl Solution {
    /// 返回卡车可以行驶的最大距离（单位：km）。
    ///
    /// 核心思路：
    /// - 基础距离 = main_tank * 10；
    /// - 每消耗 5L 且副油箱有油时，会从副油箱补充 1L 到主油箱，
    ///   相当于主油箱净消耗 4L，额外获得 10km；
    /// - 最多可触发 min(additional_tank, (main_tank - 1) / 4) 次补充。
    pub fn distance_traveled(main_tank: i32, additional_tank: i32) -> i32 {
        let transfers = additional_tank.min((main_tank - 1) / 4);
        (main_tank + transfers) * 10
    }
}
```
