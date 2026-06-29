---
title: "leetcode-有序集合23"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 设计数字容器系统

设计一个数字容器系统，可以实现以下功能：

· 在系统中给定下标处 插入 或者 替换 一个数字。
· 返回 系统中给定数字的最小下标。

请你实现一个 NumberContainers 类：

· NumberContainers() 初始化数字容器系统。
· void change(int index, int number) 在下标 index 处填入 number 。如果该下标 index 处已经有数字了，那么用 number 替换该数字。
· int find(int number) 返回给定数字 number 在系统中的最小下标。如果系统中没有 number ，那么返回 -1 。


```
use std::collections::{HashMap, BinaryHeap};
use std::cmp::Reverse;

/// 数字容器系统
///
/// 支持在指定下标插入/替换数字，并查询数字的最小下标
///
/// # 时间复杂度
/// - change: O(log n)
/// - find: O(k log n)，k 为失效元素数量（均摊 O(log n)）
///
/// # 空间复杂度
/// O(n)
pub struct NumberContainers {
    /// 存储每个下标对应的数字
    index_to_num: HashMap<i32, i32>,
    /// 存储每个数字的所有下标（最小堆）
    num_to_indices: HashMap<i32, BinaryHeap<Reverse<i32>>>,
}

impl NumberContainers {
    /// 初始化数字容器系统
    pub fn new() -> Self {
        Self {
            index_to_num: HashMap::new(),
            num_to_indices: HashMap::new(),
        }
    }

    /// 在下标处填入数字（如果已存在则替换）
    ///
    /// # 参数
    /// - `index`: 要填入的下标
    /// - `number`: 要填入的数字
    pub fn change(&mut self, index: i32, number: i32) {
        // 更新下标到数字的映射
        self.index_to_num.insert(index, number);

        // 将下标加入对应数字的堆中
        self.num_to_indices
            .entry(number)
            .or_insert_with(BinaryHeap::new)
            .push(Reverse(index));
    }

    /// 返回指定数字的最小下标
    ///
    /// # 参数
    /// - `number`: 要查询的数字
    ///
    /// # 返回
    /// 最小下标，如果不存在则返回 -1
    pub fn find(&mut self, number: i32) -> i32 {
        // 获取该数字对应的堆
        let heap = match self.num_to_indices.get_mut(&number) {
            Some(h) => h,
            None => return -1,
        };

        // 清理堆顶的失效下标
        while let Some(&Reverse(index)) = heap.peek() {
            // 检查该下标是否仍然对应这个数字
            if self.index_to_num.get(&index) == Some(&number) {
                return index;
            }
            // 失效，弹出并继续检查
            heap.pop();
        }

        -1
    }
}
```
