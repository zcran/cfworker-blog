---
title: "leetcode-线段树3"
date: 2026-06-19T09:37:24+08:00
tags: ["leetcode", "线段树"]
draft: false
---


## 区间内查询数字的频率

请你设计一个数据结构，它能求出给定子数组内一个给定值的 频率 。

子数组中一个值的 频率 指的是这个子数组中这个值的出现次数。

请你实现 RangeFreqQuery 类：

· RangeFreqQuery(int[] arr) 用下标从 0 开始的整数数组 arr 构造一个类的实例。
· int query(int left, int right, int value) 返回子数组 arr[left...right] 中 value 的 频率 。

一个 子数组 指的是数组中一段连续的元素。arr[left...right] 指的是 nums 中包含下标 left 和 right 在内 的中间一段连续元素。


```
use std::collections::HashMap;

/// RangeFreqQuery 支持查询子数组内某个值的出现频率
///
/// 核心思路：预处理时，将每个值的所有出现下标存入数组。
/// 查询时，在对应值的下标数组中二分查找 left 和 right，
/// 两者下标之差即为频率。
pub struct RangeFreqQuery {
    /// 每个值对应的所有出现下标（已有序）
    positions: HashMap<i32, Vec<usize>>,
}

impl RangeFreqQuery {
    /// 用数组 arr 构造查询器
    pub fn new(arr: Vec<i32>) -> Self {
        let mut positions = HashMap::with_capacity(arr.len());
        for (i, val) in arr.into_iter().enumerate() {
            positions.entry(val).or_insert_with(Vec::new).push(i);
        }
        RangeFreqQuery { positions }
    }

    /// 查询子数组 arr[left..=right] 中 value 的出现次数
    pub fn query(&self, left: i32, right: i32, value: i32) -> i32 {
        let pos = match self.positions.get(&value) {
            Some(p) => p,
            None => return 0,
        };

        // 在有序下标数组中，找到第一个 >= left 的位置
        let l = pos.partition_point(|&p| p < left as usize);
        // 找到第一个 > right 的位置
        let r = pos.partition_point(|&p| p <= right as usize);
        (r - l) as i32
    }
}
```
