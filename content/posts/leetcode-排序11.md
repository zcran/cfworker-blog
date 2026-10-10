---
title: "leetcode-排序11"
date: 2026-10-05T00:35:00+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 最小时间差

给定一个 24 小时制（小时:分钟 "HH:MM"）的时间列表，找出列表中任意两个时间的最小时间差并以分钟数表示。


```
impl Solution {
    /// 求时间列表中任意两个时间的最小分钟差
    ///
    /// 思路：将 "HH:MM" 转为一天内的分钟数（0..1440），排序后
    /// 最小差必然出现在相邻时间之间；此外还需考虑跨午夜的情况
    /// （首个时间 + 1440 减去最后一个时间）。
    /// 若时间点数量超过一天的总分钟数 1440，由鸽巢原理必然有重复，直接返回 0。
    pub fn find_min_difference(time_points: Vec<String>) -> i32 {
        // 超过 1440 个时间点必有重复，最小差为 0
        if time_points.len() > 1440 {
            return 0;
        }

        // "HH:MM" -> 当天分钟数
        let mut minutes: Vec<i32> = time_points
            .iter()
            .map(|t| {
                let h = t[0..2].parse::<i32>().unwrap();
                let m = t[3..5].parse::<i32>().unwrap();
                h * 60 + m
            })
            .collect();
        minutes.sort_unstable();

        // 相邻时间的最小差
        let mut min_diff = i32::MAX;
        for w in minutes.windows(2) {
            min_diff = min_diff.min(w[1] - w[0]);
        }

        // 跨午夜：首时间 + 1440 - 末时间
        let wrap = minutes[0] + 1440 - minutes[minutes.len() - 1];
        min_diff.min(wrap)
    }
}
```
