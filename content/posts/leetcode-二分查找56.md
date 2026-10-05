---
title: "leetcode-二分查找56"
date: 2026-09-28T12:29:14+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 完成旅途的最少时间

给你一个数组 time ，其中 time[i] 表示第 i 辆公交车完成 一趟旅途 所需要花费的时间。

每辆公交车可以 连续 完成多趟旅途，也就是说，一辆公交车当前旅途完成后，可以 立马开始 下一趟旅途。每辆公交车 独立 运行，也就是说可以同时有多辆公交车在运行且互不影响。

给你一个整数 totalTrips ，表示所有公交车 总共 需要完成的旅途数目。请你返回完成 至少 totalTrips 趟旅途需要花费的 最少 时间。


```
impl Solution {
    pub fn minimum_time(time: Vec<i32>, total_trips: i32) -> i64 {
        let total = total_trips as i64;

        // 判断 t 时间内能否完成至少 total 趟旅途
        let feasible = |t: i64| -> bool {
            let mut trips = 0i64;
            for &period in &time {
                trips += t / period as i64;
                if trips >= total {
                    return true;
                }
            }
            false
        };

        // 时间下界为 1，上界为「只用最慢的车跑完所有趟」所需时间
        let mut lo = 1i64;
        let mut hi = total * *time.iter().max().unwrap() as i64;
        while lo < hi {
            let mid = lo + (hi - lo) / 2;
            if feasible(mid) {
                hi = mid;
            } else {
                lo = mid + 1;
            }
        }
        lo
    }
}
```
