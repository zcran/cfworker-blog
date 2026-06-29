---
title: "leetcode-单调栈15"
date: 2026-06-07T10:14:04+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 车队

在一条单行道上，有 n 辆车开往同一目的地。目的地是几英里以外的 target 。

给定两个整数数组 position 和 speed ，长度都是 n ，其中 position[i] 是第 i 辆车的位置， speed[i] 是第 i 辆车的速度(单位是英里/小时)。

一辆车永远不会超过前面的另一辆车，但它可以追上去，并以较慢车的速度在另一辆车旁边行驶。

车队 是指并排行驶的一辆或几辆汽车。车队的速度是车队中 最慢 的车的速度。

即便一辆车在 target 才赶上了一个车队，它们仍然会被视作是同一个车队。

返回到达目的地的车队数量 。


```
impl Solution {
    /// 车队问题 - 计算到达目标的车队数量
    ///
    /// 核心思路：按位置降序排序，计算每辆车到达终点的时间
    /// 如果后车用时 > 前车用时，则后车追不上前车，形成新车队
    /// 否则后车会被前车阻挡，属于同一车队
    pub fn car_fleet(target: i32, position: Vec<i32>, speed: Vec<i32>) -> i32 {
        let mut cars: Vec<(i32, i32)> = position.into_iter().zip(speed).collect();
        // 按位置降序排序（离终点近的在前）
        cars.sort_unstable_by(|a, b| b.0.cmp(&a.0));

        let mut fleets = 0;
        let mut slowest_time = 0.0;

        for (pos, spd) in cars {
            // 到达终点所需时间
            let time = (target - pos) as f64 / spd as f64;

            // 当前车比前面所有车都慢，会形成新车队
            if time > slowest_time {
                fleets += 1;
                slowest_time = time;
            }
            // 否则当前车会被前面的慢车阻挡，并入已有车队
        }

        fleets
    }
}
```
