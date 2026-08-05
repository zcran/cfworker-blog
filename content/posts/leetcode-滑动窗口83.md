---
title: "leetcode-滑动窗口83"
date: 2026-07-18T11:02:34+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 将数组分成最小总代价的子数组 II

给你一个下标从 0 开始长度为 n 的整数数组 nums 和两个 正 整数 k 和 dist 。

一个数组的 代价 是数组中的 第一个 元素。比方说，[1,2,3] 的代价为 1 ，[3,4,1] 的代价为 3 。

你需要将 nums 分割成 k 个 连续且互不相交 的子数组，满足 第二 个子数组与第 k 个子数组中第一个元素的下标距离 不超过 dist 。换句话说，如果你将 nums 分割成子数组 nums[0..(i1 - 1)], nums[i1..(i2 - 1)], ..., nums[ik-1..(n - 1)] ，那么它需要满足 ik-1 - i1 <= dist 。

请你返回这些子数组的 最小 总代价。


```
use std::collections::BTreeMap;

/// 维护动态数据流的前 k 小元素和
struct TopKSum {
    k: usize,              // 需要维护前 k 小
    small: BTreeMap<i32, i32>, // 前 k 小元素（多集）
    large: BTreeMap<i32, i32>, // 剩余元素（多集）
    small_sum: i64,        // small 中元素和
    small_size: usize,     // small 中元素个数
    large_size: usize,     // large 中元素个数
}

impl TopKSum {
    fn new(k: usize) -> Self {
        Self {
            k,
            small: BTreeMap::new(),
            large: BTreeMap::new(),
            small_sum: 0,
            small_size: 0,
            large_size: 0,
        }
    }

    /// 向 multiset 中添加一个元素
    fn add_to_map(map: &mut BTreeMap<i32, i32>, key: i32) {
        *map.entry(key).or_insert(0) += 1;
    }

    /// 从 multiset 中移除一个元素，返回是否成功
    fn remove_from_map(map: &mut BTreeMap<i32, i32>, key: i32) -> bool {
        if let Some(count) = map.get_mut(&key) {
            *count -= 1;
            if *count == 0 {
                map.remove(&key);
            }
            true
        } else {
            false
        }
    }

    /// 获取 multiset 的最小键
    fn min_key(map: &BTreeMap<i32, i32>) -> Option<i32> {
        map.keys().next().copied()
    }

    /// 获取 multiset 的最大键
    fn max_key(map: &BTreeMap<i32, i32>) -> Option<i32> {
        map.keys().next_back().copied()
    }

    /// 调整两个集合，保证 small 恰好包含前 k 小元素
    fn balance(&mut self) {
        // 如果 small 不足 k 个，从 large 移入最小元素
        while self.small_size < self.k && !self.large.is_empty() {
            if let Some(x) = Self::min_key(&self.large) {
                Self::add_to_map(&mut self.small, x);
                Self::remove_from_map(&mut self.large, x);
                self.small_sum += x as i64;
                self.small_size += 1;
                self.large_size -= 1;
            }
        }

        // 如果 small 超过 k 个，移出最大元素到 large
        while self.small_size > self.k {
            if let Some(x) = Self::max_key(&self.small) {
                Self::add_to_map(&mut self.large, x);
                Self::remove_from_map(&mut self.small, x);
                self.small_sum -= x as i64;
                self.small_size -= 1;
                self.large_size += 1;
            }
        }
    }

    /// 插入元素 x
    fn insert(&mut self, x: i32) {
        // 如果 large 非空且 x 大于等于 large 的最小值，插入 large
        if let Some(min_large) = Self::min_key(&self.large) {
            if x >= min_large {
                Self::add_to_map(&mut self.large, x);
                self.large_size += 1;
            } else {
                Self::add_to_map(&mut self.small, x);
                self.small_sum += x as i64;
                self.small_size += 1;
            }
        } else {
            // large 为空，直接插入 small
            Self::add_to_map(&mut self.small, x);
            self.small_sum += x as i64;
            self.small_size += 1;
        }
        self.balance();
    }

    /// 删除元素 x
    fn remove(&mut self, x: i32) {
        if Self::remove_from_map(&mut self.small, x) {
            self.small_sum -= x as i64;
            self.small_size -= 1;
        } else {
            Self::remove_from_map(&mut self.large, x);
            self.large_size -= 1;
        }
        self.balance();
    }

    /// 返回前 k 小元素的和
    fn sum(&self) -> i64 {
        self.small_sum
    }
}

impl Solution {
    pub fn minimum_cost(nums: Vec<i32>, k: i32, dist: i32) -> i64 {
        let n = nums.len();
        let k = k as usize;
        let dist = dist as usize;

        // 第一个子数组固定包含 nums[0]
        // 需要从剩余元素中选择 k-1 个作为子数组的起始位置
        // 问题转化为：在窗口 [1, i] 中选 k-2 个最小元素，加上 nums[i] 作为第 k 个子数组起始
        let mut selector = TopKSum::new(k - 2);

        // 初始化窗口：包含 nums[1..k-1]（前 k-1 个子数组起始的候选）
        for i in 1..k - 1 {
            selector.insert(nums[i]);
        }

        // 初始答案：前 k 个子数组起始位置为 0, 1, ..., k-1
        let mut ans = selector.sum() + nums[k - 1] as i64;

        // 滑动窗口，i 表示第 k 个子数组的起始位置
        for i in k..n {
            // 窗口左边界：i - dist - 1，需要移除
            let left = i as i32 - dist as i32 - 1;
            if left > 0 {
                selector.remove(nums[left as usize]);
            }

            // 加入新元素 nums[i-1] 到窗口
            selector.insert(nums[i - 1]);

            // 更新答案：前 k-2 个最小和 + nums[i]
            ans = ans.min(selector.sum() + nums[i] as i64);
        }

        ans + nums[0] as i64
    }
}
```
