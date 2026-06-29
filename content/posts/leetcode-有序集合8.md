---
title: "leetcode-有序集合8"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 我的日程安排表 III

当 k 个日程存在一些非空交集时（即, k 个日程包含了一些相同时间），就会产生 k 次预订。

给你一些日程安排 [startTime, endTime) ，请你在每个日程安排添加后，返回一个整数 k ，表示所有先前日程安排会产生的最大 k 次预订。

实现一个 MyCalendarThree 类来存放你的日程安排，你可以一直添加新的日程安排。

· MyCalendarThree() 初始化对象。

· int book(int startTime, int endTime) 返回一个整数 k ，表示日历中存在的 k 次预订的最大值。


```
use std::collections::BTreeMap;

/// 日程日历 - 追踪最大重叠预订次数
///
/// # 算法思路
/// 使用差分数组思想，用 BTreeMap 离散化存储事件：
/// - 区间开始处 +1（预订开始）
/// - 区间结束处 -1（预订结束）
/// - 按时间顺序累加，记录最大重叠数
///
/// # 复杂度
/// - 时间复杂度: O(n²)，每次 book 需要遍历所有事件点
/// - 空间复杂度: O(n)，存储所有事件点
///
/// # 示例
/// ```
/// let mut calendar = MyCalendarThree::new();
/// assert_eq!(calendar.book(10, 20), 1);
/// assert_eq!(calendar.book(50, 60), 1);
/// assert_eq!(calendar.book(10, 40), 2);
/// assert_eq!(calendar.book(5, 15), 3);
/// assert_eq!(calendar.book(5, 10), 3);
/// ```
struct MyCalendarThree {
    /// 差分事件映射：时间点 -> 变化量
    /// 正数表示预订开始，负数表示预订结束
    events: BTreeMap<i32, i32>,
}

impl MyCalendarThree {
    /// 创建新的日历
    pub fn new() -> Self {
        Self {
            events: BTreeMap::new(),
        }
    }

    /// 添加新的日程安排并返回当前最大重叠数
    ///
    /// # 参数
    /// - `start_time`: 日程开始时间（包含）
    /// - `end_time`: 日程结束时间（不包含）
    ///
    /// # 返回
    /// 当前所有日程的最大重叠次数
    pub fn book(&mut self, start_time: i32, end_time: i32) -> i32 {
        // 1. 添加差分事件
        *self.events.entry(start_time).or_insert(0) += 1;
        *self.events.entry(end_time).or_insert(0) -= 1;

        // 2. 扫描计算当前最大重叠数
        let mut current = 0;
        let mut max_k = 0;

        for &delta in self.events.values() {
            current += delta;
            max_k = max_k.max(current);
        }

        max_k
    }
}
```
