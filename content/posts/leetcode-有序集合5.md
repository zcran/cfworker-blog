---
title: "leetcode-有序集合5"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## Range 模块

Range模块是跟踪数字范围的模块。设计一个数据结构来跟踪表示为 半开区间 的范围并查询它们。

半开区间 [left, right) 表示所有 left <= x < right 的实数 x 。

实现 RangeModule 类:

· RangeModule() 初始化数据结构的对象。
· void addRange(int left, int right) 添加 半开区间 [left, right)，跟踪该区间中的每个实数。添加与当前跟踪的数字部分重叠的区间时，应当添加在区间 [left, right) 中尚未跟踪的任何数字到该区间中。
· boolean queryRange(int left, int right) 只有在当前正在跟踪区间 [left, right) 中的每一个实数时，才返回 true ，否则返回 false 。
· void removeRange(int left, int right) 停止跟踪 半开区间 [left, right) 中当前正在跟踪的每个实数。


```
use std::collections::BTreeMap;

/// 范围模块 - 使用 BTreeMap 维护不相交的区间
///
/// 每个区间存储为 [start, end)，所有区间按 start 排序且互不重叠
///
/// # 复杂度
/// - add_range: O(k + log n)，k 为重叠区间数量
/// - query_range: O(log n + k)
/// - remove_range: O(k + log n)
struct RangeModule {
    intervals: BTreeMap<i32, i32>, // start -> end (半开区间 [start, end))
}

impl RangeModule {
    /// 初始化空的范围模块
    pub fn new() -> Self {
        Self {
            intervals: BTreeMap::new(),
        }
    }

    /// 添加范围 [left, right)，自动合并重叠区间
    ///
    /// # 示例
    /// ```
    /// let mut rm = RangeModule::new();
    /// rm.add_range(1, 5);
    /// rm.add_range(3, 8); // 合并为 [1, 8)
    /// ```
    pub fn add_range(&mut self, mut left: i32, mut right: i32) {
        // 找到第一个右端点 >= left 的区间（可能重叠）
        let start_key = self.intervals.range(..=left).next_back();
        let mut to_remove = Vec::new();

        // 检查前一个区间是否与当前区间重叠
        if let Some((&prev_start, &prev_end)) = start_key {
            if prev_end >= left {
                // 合并：扩展左边界
                left = left.min(prev_start);
                right = right.max(prev_end);
                to_remove.push(prev_start);
            }
        }

        // 检查后续所有重叠的区间
        for (&start, &end) in self.intervals.range(left..=right) {
            if start > right {
                break;
            }
            right = right.max(end);
            to_remove.push(start);
        }

        // 删除所有被合并的旧区间
        for start in to_remove {
            self.intervals.remove(&start);
        }

        // 插入合并后的新区间
        self.intervals.insert(left, right);
    }

    /// 查询范围 [left, right) 是否完全被覆盖
    ///
    /// # 返回
    /// - `true`: 范围内的所有点都被跟踪
    /// - `false`: 存在未被跟踪的点
    ///
    /// # 示例
    /// ```
    /// let mut rm = RangeModule::new();
    /// rm.add_range(1, 10);
    /// assert!(rm.query_range(3, 7));   // true
    /// assert!(!rm.query_range(5, 12)); // false
    /// ```
    pub fn query_range(&self, left: i32, right: i32) -> bool {
        // 找到最后一个起始点 <= left 的区间
        let Some((&start, &end)) = self.intervals.range(..=left).next_back() else {
            return false;
        };

        // 检查该区间是否完全覆盖 [left, right)
        start <= left && end >= right
    }

    /// 移除范围 [left, right)，只移除该范围内被跟踪的部分
    ///
    /// # 示例
    /// ```
    /// let mut rm = RangeModule::new();
    /// rm.add_range(1, 10);
    /// rm.remove_range(3, 7);  // 剩下 [1, 3) 和 [7, 10)
    /// ```
    pub fn remove_range(&mut self, left: i32, right: i32) {
        let mut to_remove = Vec::new();
        let mut to_insert = Vec::new();

        // 找到所有与 [left, right) 重叠的区间
        for (&start, &end) in self.intervals.range(..right) {
            if end <= left {
                continue; // 完全在左侧，不重叠
            }
            if start >= right {
                break; // 后续区间都在右侧，停止
            }

            // 区间与 [left, right) 重叠，需要处理
            to_remove.push(start);

            // 检查是否有左半部分需要保留
            if start < left && end > left {
                to_insert.push((start, left));
            }

            // 检查是否有右半部分需要保留
            if end > right && start < right {
                to_insert.push((right, end));
            }

            // 注意：如果区间被完全包含在 [left, right) 中，则整段删除
            // 此时没有需要保留的部分
        }

        // 执行删除和插入操作
        for start in to_remove {
            self.intervals.remove(&start);
        }

        for (new_start, new_end) in to_insert {
            self.intervals.insert(new_start, new_end);
        }
    }
}
```
