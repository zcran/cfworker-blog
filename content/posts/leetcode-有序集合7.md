---
title: "leetcode-有序集合7"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 我的日程安排表 II

实现一个程序来存放你的日程安排。如果要添加的时间内不会导致三重预订时，则可以存储这个新的日程安排。

当三个日程安排有一些时间上的交叉时（例如三个日程安排都在同一时间内），就会产生 三重预订。

事件能够用一对整数 startTime 和 endTime 表示，在一个半开区间的时间 [startTime, endTime) 上预定。实数 x 的范围为  startTime <= x < endTime。

实现 MyCalendarTwo 类：

· MyCalendarTwo() 初始化日历对象。
· boolean book(int startTime, int endTime) 如果可以将日程安排成功添加到日历中而不会导致三重预订，返回 true。否则，返回 false 并且不要将该日程安排添加到日历中。


```
use std::collections::BTreeMap;

/// 日程日历 - 允许最多两次重叠预订
///
/// # 算法思路
/// 使用差分数组思想，但用 BTreeMap 实现离散化：
/// - 在区间开始处 +1，结束处 -1
/// - 遍历时计算当前重叠数，如果任何点重叠数 >= 3 则预订失败
///
/// 半开区间 [start, end)，支持实数范围
///
/// # 复杂度
/// - 时间复杂度: O(n²)，n 为预订次数（遍历所有事件点）
/// - 空间复杂度: O(n)
///
/// # 示例
/// ```
/// let mut calendar = MyCalendarTwo::new();
/// assert!(calendar.book(10, 20)); // true
/// assert!(calendar.book(50, 60)); // true
/// assert!(calendar.book(10, 40)); // true (最多重叠2次)
/// assert!(!calendar.book(5, 15)); // false (会变成3次重叠)
/// ```
struct MyCalendarTwo {
    events: BTreeMap<i32, i32>, // 时间点 -> 事件变化量
}

impl MyCalendarTwo {
    /// 创建新的日历
    pub fn new() -> Self {
        Self {
            events: BTreeMap::new(),
        }
    }

    /// 尝试预订新日程 [start, end)
    ///
    /// # 返回
    /// - `true`: 预订成功，不会导致三重预订
    /// - `false`: 会导致三重预订，未添加
    pub fn book(&mut self, start: i32, end: i32) -> bool {
        // 1. 临时添加事件
        *self.events.entry(start).or_insert(0) += 1;
        *self.events.entry(end).or_insert(0) -= 1;

        // 2. 检查是否会导致三重预订
        let mut ongoing = 0;
        let mut is_valid = true;

        for &delta in self.events.values() {
            ongoing += delta;
            if ongoing >= 3 {
                is_valid = false;
                break;
            }
        }

        // 3. 如果无效，回滚修改
        if !is_valid {
            self.rollback(start, end);
        }

        is_valid
    }

    /// 回滚失败的事件
    fn rollback(&mut self, start: i32, end: i32) {
        // 减少开始点的计数
        if let Some(count) = self.events.get_mut(&start) {
            *count -= 1;
            if *count == 0 {
                self.events.remove(&start);
            }
        }

        // 减少结束点的计数
        if let Some(count) = self.events.get_mut(&end) {
            *count += 1; // 因为之前减了1，现在要加回来
            if *count == 0 {
                self.events.remove(&end);
            }
        }
    }
}
```
