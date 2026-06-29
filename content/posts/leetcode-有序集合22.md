---
title: "leetcode-有序集合22"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 无限集中的最小数字

现有一个包含所有正整数的集合 [1, 2, 3, 4, 5, ...] 。

实现 SmallestInfiniteSet 类：

· SmallestInfiniteSet() 初始化 SmallestInfiniteSet 对象以包含 所有 正整数。
· int popSmallest() 移除 并返回该无限集中的最小整数。
· void addBack(int num) 如果正整数 num 不 存在于无限集中，则将一个 num 添加 到该无限集中。


```
use std::collections::BTreeSet;

/// 无限正整数集合，支持弹出最小元素和添加回元素
///
/// # 思路
/// 使用 BTreeSet 存储被添加回来的元素，用指针维护未被弹出的最小整数
///
/// # 时间复杂度
/// - new: O(1)
/// - pop_smallest: O(log n)
/// - add_back: O(log n)
///
/// # 空间复杂度
/// O(k)，其中 k 是被添加回来的元素数量
struct SmallestInfiniteSet {
    /// 被添加回来的元素集合（自动排序）
    added_back: BTreeSet<i32>,
    /// 下一个未被弹出的最小正整数
    next: i32,
}

impl SmallestInfiniteSet {
    /// 初始化无限正整数集合
    pub fn new() -> Self {
        Self {
            added_back: BTreeSet::new(),
            next: 1,
        }
    }

    /// 移除并返回集合中的最小元素
    pub fn pop_smallest(&mut self) -> i32 {
        // 如果存在被添加回来的元素，它们比 next 更小
        if let Some(&smallest) = self.added_back.iter().next() {
            // 如果被添加回来的最小元素小于 next，返回它
            if smallest < self.next {
                self.added_back.remove(&smallest);
                return smallest;
            }
        }

        // 否则返回 next 并递增
        let result = self.next;
        self.next += 1;
        result
    }

    /// 如果 num 不在集合中，将其添加回集合
    pub fn add_back(&mut self, num: i32) {
        // 只有小于 next 的 num 才需要添加
        // 因为大于等于 next 的数都还在集合中
        if num < self.next {
            self.added_back.insert(num);
        }
    }
}

/// 极简版本（使用 BTreeSet 存储所有元素）
/// 适用于 LeetCode 题目范围（最多 1000 次操作）
impl SmallestInfiniteSet {
    /// 使用 BTreeSet 存储所有可用元素
    /// 性能稍低但实现更直观
    pub fn new_simple() -> Self {
        let mut set = BTreeSet::new();
        // 预分配足够大的范围
        for i in 1..=1000 {
            set.insert(i);
        }
        SmallestInfiniteSet { added_back: set, next: 1001 }
    }
}
```
