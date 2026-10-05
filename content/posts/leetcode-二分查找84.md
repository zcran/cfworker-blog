---
title: "leetcode-二分查找84"
date: 2026-09-28T12:29:16+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 完成所有送货任务的最少时间

给你两个大小为 2 的整数数组：d = [d1, d2] 和 r = [r1, r2]。

两架送货无人机负责完成特定数量的送货任务。无人机 i 必须完成 di 次送货。

每次送货花费 正好 一小时，并且在任何给定小时内 只有一架 无人机可以送货。

此外，两架无人机都需要在特定时间间隔进行充电，在此期间它们不能送货。无人机 i 必须每 ri 小时充电一次（即在 ri 的倍数小时进行充电）。

返回完成所有送货所需的 最小 总时间（以小时为单位）的整数。


```
impl Solution {
    pub fn minimum_time(d: Vec<i32>, r: Vec<i32>) -> i64 {
        let (d1, d2) = (d[0] as i64, d[1] as i64);
        let (r1, r2) = (r[0] as i64, r[1] as i64);

        // 两架无人机同时充电的周期
        let l = r1 / Self::gcd(r1, r2) * r2;

        // 判断在 t 小时内能否完成所有送货：
        // 无人机 1 可用 t - t/r1 小时，无人机 2 可用 t - t/r2 小时，
        // 且总可用时间不超过 t - t/l（扣除两者同时充电的小时）
        let can_finish = |t: i64| -> bool {
            d1 <= t - t / r1 && d2 <= t - t / r2 && d1 + d2 <= t - t / l
        };

        // 二分最小时间，上界为所有任务串行完成的最坏情况
        let mut left = d1 + d2 - 1;
        let mut right = (d1 + d2) * 2;
        while left + 1 < right {
            let mid = left + (right - left) / 2;
            if can_finish(mid) {
                right = mid;
            } else {
                left = mid;
            }
        }
        right
    }

    fn gcd(a: i64, b: i64) -> i64 {
        if b == 0 { a } else { Self::gcd(b, a % b) }
    }
}
```
