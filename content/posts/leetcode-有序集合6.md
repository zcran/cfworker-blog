---
title: "leetcode-有序集合6"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 我的日程安排表 I


实现一个 MyCalendar 类来存放你的日程安排。如果要添加的日程安排不会造成 重复预订 ，则可以存储这个新的日程安排。

当两个日程安排有一些时间上的交叉时（例如两个日程安排都在同一时间内），就会产生 重复预订 。

日程可以用一对整数 startTime 和 endTime 表示，这里的时间是半开区间，即 [startTime, endTime), 实数 x 的范围为，  startTime <= x < endTime 。

实现 MyCalendar 类：

· MyCalendar() 初始化日历对象。
· boolean book(int startTime, int endTime) 如果可以将日程安排成功添加到日历中而不会导致重复预订，返回 true 。否则，返回 false 并且不要将该日程安排添加到日历中。


```
use std::collections::BTreeMap;

/// 日程日历 - 检测并防止重复预订
///
/// 使用 BTreeMap 维护已预订的区间 [start, end)，
/// 所有区间互不重叠且按起始时间排序。
///
/// # 算法思路
/// 对于新日程 [start, end)，只需检查前一个区间是否与它重叠：
/// - 找到最后一个起始时间 < end 的区间
/// - 如果该区间的结束时间 > start，则产生重叠
///
/// # 复杂度
/// - 时间复杂度: O(log n)，n 为已预订日程数量
/// - 空间复杂度: O(n)
///
/// # 示例
/// ```
/// let mut calendar = MyCalendar::new();
/// assert!(calendar.book(10, 20)); // true
/// assert!(!calendar.book(15, 25)); // false (重叠)
/// assert!(calendar.book(20, 30)); // true (边界不重叠)
/// ```
#[derive(Default)]
struct MyCalendar {
    // 存储已预订的日程: start -> end (半开区间 [start, end))
    bookings: BTreeMap<i32, i32>,
}

impl MyCalendar {
    /// 创建新的空日历
    pub fn new() -> Self {
        Self::default()
    }

    /// 尝试预订新日程 [start_time, end_time)
    ///
    /// # 返回
    /// - `true`: 预订成功，日程已添加
    /// - `false`: 与现有日程冲突，未添加
    pub fn book(&mut self, start_time: i32, end_time: i32) -> bool {
        // 边界检查：无效区间直接返回 false
        if start_time >= end_time {
            return false;
        }

        // 查找最后一个起始时间 < end_time 的日程
        // 只有这个日程可能与 [start_time, end_time) 重叠
        if let Some((_, &prev_end)) = self.bookings.range(..end_time).next_back() {
            // 如果前一个日程的结束时间 > 新日程的开始时间，则重叠
            if prev_end > start_time {
                return false;
            }
        }

        // 无冲突，插入新日程
        self.bookings.insert(start_time, end_time);
        true
    }
}
```
