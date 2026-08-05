---
title: "leetcode-滑动窗口101"
date: 2026-07-18T11:02:35+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 计算子数组的 x-sum II

给你一个由 n 个整数组成的数组 nums，以及两个整数 k 和 x。

数组的 x-sum 计算按照以下步骤进行：

统计数组中所有元素的出现次数。
仅保留出现频率最高的前 x 种元素。如果两种元素的出现次数相同，则数值 较大 的元素被认为出现次数更多。
计算结果数组的和。
注意，如果数组中的不同元素少于 x 个，则其 x-sum 是数组的元素总和。

返回一个长度为 n - k + 1 的整数数组 answer，其中 answer[i] 是 子数组 nums[i..i + k - 1] 的 x-sum。

子数组 是数组内的一个连续 非空 的元素序列。


```
use std::collections::{BTreeSet, HashMap};
use std::cmp::Ordering;

/// 表示一个元素及其在当前窗口中的出现频率
/// 用于在 BTreeSet 中排序：频率高的优先，频率相同则值大的优先
#[derive(Eq, PartialEq, Clone, Debug)]
struct Element {
    freq: i32,
    value: i32,
}

impl Ord for Element {
    fn cmp(&self, other: &Self) -> Ordering {
        // 注意：比较顺序为降序（频率高、值大的排在前面）
        if self.freq != other.freq {
            self.freq.cmp(&other.freq)
        } else {
            self.value.cmp(&other.value)
        }
    }
}

impl PartialOrd for Element {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        Some(self.cmp(other))
    }
}

/// 维护滑动窗口，支持动态插入/删除元素，并实时计算 x-sum
struct WindowState {
    x: usize,                         // 保留前 x 个高频元素
    sum: i64,                         // 当前窗口的 x-sum
    top: BTreeSet<Element>,           // 前 x 个高频元素（有序集合）
    rest: BTreeSet<Element>,          // 其余元素
    freq: HashMap<i32, i32>,          // 当前窗口内每个元素的频率
}

impl WindowState {
    /// 创建新的窗口状态
    fn new(x: i32) -> Self {
        WindowState {
            x: x as usize,
            sum: 0,
            top: BTreeSet::new(),
            rest: BTreeSet::new(),
            freq: HashMap::new(),
        }
    }

    /// 向窗口中插入一个元素
    fn insert(&mut self, value: i32) {
        // 如果元素已存在，先移除旧频率的记录
        if let Some(&count) = self.freq.get(&value) {
            if count > 0 {
                self.remove_internal(Element { freq: count, value });
            }
        }

        // 更新频率并插入新记录
        *self.freq.entry(value).or_insert(0) += 1;
        let new_count = self.freq[&value];
        self.insert_internal(Element { freq: new_count, value });
    }

    /// 从窗口中移除一个元素
    fn remove(&mut self, value: i32) {
        let count = self.freq[&value];

        // 移除旧频率的记录
        self.remove_internal(Element { freq: count, value });

        // 更新频率
        *self.freq.get_mut(&value).unwrap() -= 1;
        if self.freq[&value] > 0 {
            let new_count = self.freq[&value];
            self.insert_internal(Element { freq: new_count, value });
        } else {
            // 频率降为 0，从哈希表中移除
            self.freq.remove(&value);
        }
    }

    /// 获取当前的 x-sum
    fn get_sum(&self) -> i64 {
        self.sum
    }

    /// 内部方法：将元素插入到 top 或 rest 集合
    fn insert_internal(&mut self, elem: Element) {
        // 判断是否应放入 top 集合
        let top_is_empty = self.top.len() < self.x;
        let should_be_top = top_is_empty || elem > *self.top.iter().next().unwrap();

        if should_be_top {
            // 加入 top 集合
            self.sum += elem.freq as i64 * elem.value as i64;
            self.top.insert(elem.clone());

            // 如果 top 集合超出容量，将最小的元素移到 rest
            if self.top.len() > self.x {
                let moved = self.top.iter().next().unwrap().clone();
                self.sum -= moved.freq as i64 * moved.value as i64;
                self.top.remove(&moved);
                self.rest.insert(moved);
            }
        } else {
            // 直接放入 rest 集合
            self.rest.insert(elem);
        }
    }

    /// 内部方法：从 top 或 rest 集合移除元素
    fn remove_internal(&mut self, elem: Element) {
        let is_in_top = self.top.contains(&elem);

        if is_in_top {
            // 从 top 集合移除
            self.sum -= elem.freq as i64 * elem.value as i64;
            self.top.remove(&elem);

            // 如果 rest 集合中有元素，将最大的提升到 top
            if let Some(promoted) = self.rest.iter().next_back().cloned() {
                self.sum += promoted.freq as i64 * promoted.value as i64;
                self.rest.remove(&promoted);
                self.top.insert(promoted);
            }
        } else {
            // 从 rest 集合移除
            self.rest.remove(&elem);
        }
    }
}

impl Solution {
    pub fn find_x_sum(nums: Vec<i32>, k: i32, x: i32) -> Vec<i64> {
        let k = k as usize;
        let mut window = WindowState::new(x);
        let mut answer = Vec::with_capacity(nums.len() - k + 1);

        // 滑动窗口遍历数组
        for i in 0..nums.len() {
            // 1. 添加右端元素
            window.insert(nums[i]);

            // 2. 窗口已满，移除左端元素
            if i >= k {
                window.remove(nums[i - k]);
            }

            // 3. 窗口形成后，记录结果
            if i >= k - 1 {
                answer.push(window.get_sum());
            }
        }

        answer
    }
}
```
