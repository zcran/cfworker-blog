---
title: "leetcode-有序集合21"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 统计区间中的整数数目

给你区间的 空 集，请你设计并实现满足要求的数据结构：

· 新增：添加一个区间到这个区间集合中。
· 统计：计算出现在 至少一个 区间中的整数个数。

实现 CountIntervals 类：

· CountIntervals() 使用区间的空集初始化对象
· void add(int left, int right) 添加区间 [left, right] 到区间集合之中。
· int count() 返回出现在 至少一个 区间中的整数个数。

注意：区间 [left, right] 表示满足 left <= x <= right 的所有整数 x 。


```
use std::collections::BTreeMap;

/// 区间计数器
///
/// 维护一个不相交的区间集合，支持添加区间和查询总覆盖长度
struct CountIntervals {
    /// 有序映射：左端点 -> 右端点，所有区间不相交且按左端点排序
    intervals: BTreeMap<i32, i32>,
    /// 当前覆盖的整数总个数
    total: i32,
}

impl CountIntervals {
    /// 创建一个空的区间集合
    pub fn new() -> Self {
        Self {
            intervals: BTreeMap::new(),
            total: 0,
        }
    }

    /// 添加一个区间 [left, right]
    ///
    /// # 时间复杂度
    /// O(k log n)，其中 k 是合并的区间数量
    pub fn add(&mut self, mut left: i32, mut right: i32) {
        // 查找第一个可能与新区间重叠的区间
        // 即左端点 <= right 的最后一个区间
        let mut start = self.intervals.range(..=right).next_back();

        // 合并所有重叠的区间
        while let Some((&l, &r)) = start {
            // 如果不重叠，后面的区间也不会重叠（因为按左端点排序）
            if r < left {
                break;
            }

            // 扩展新区间
            left = left.min(l);
            right = right.max(r);

            // 移除旧区间并更新总计数
            self.total -= r - l + 1;
            self.intervals.remove(&l);

            // 继续检查下一个可能重叠的区间
            start = self.intervals.range(..=right).next_back();
        }

        // 插入合并后的区间
        self.total += right - left + 1;
        self.intervals.insert(left, right);
    }

    /// 返回当前覆盖的整数总个数
    ///
    /// # 时间复杂度
    /// O(1)
    pub fn count(&self) -> i32 {
        self.total
    }
}
```
