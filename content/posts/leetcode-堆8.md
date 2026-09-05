---
title: "leetcode-堆8"
date: 2026-08-21T09:54:11+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 课程表 III

这里有 n 门不同的在线课程，按从 1 到 n 编号。给你一个数组 courses ，其中 courses[i] = [durationi, lastDayi] 表示第 i 门课将会 持续 上 durationi 天课，并且必须在不晚于 lastDayi 的时候完成。

你的学期从第 1 天开始。且不能同时修读两门及两门以上的课程。

返回你最多可以修读的课程数目。


```
use std::collections::BinaryHeap;

impl Solution {
    /// 贪心策略：按 deadline 升序遍历，优先修短课；若时间不够，用当前课替换已选中最长的课
    pub fn schedule_course(mut courses: Vec<Vec<i32>>) -> i32 {
        // 过滤掉 duration > deadline 的不可能课程，按 deadline 排序
        courses.retain(|c| c[0] <= c[1]);
        courses.sort_by_key(|c| c[1]);

        let mut time = 0;
        let mut max_heap = BinaryHeap::new(); // 存储已选课程的 duration，堆顶为最大值

        for c in courses {
            let (d, ddl) = (c[0], c[1]);
            if time + d <= ddl {
                // 当前课程可以修，直接选
                time += d;
                max_heap.push(d);
            } else if let Some(&max_d) = max_heap.peek() {
                // 时间不够，尝试用更短的当前课替换已选中最长的课
                if d < max_d {
                    time = time - max_d + d;
                    max_heap.pop();
                    max_heap.push(d);
                }
            }
        }

        max_heap.len() as i32
    }
}
```
