---
title: "leetcode-数据流2"
date: 2026-04-06T20:57:54+08:00
tags: ["leetcode", "数据流"]
draft: false
---


## 将数据流变为多个不相交区间

给你一个由非负整数组成的数据流输入 a1, a2, ..., an，请你将目前为止看到的数字汇总为一组不相交的区间列表。

实现 SummaryRanges 类：

SummaryRanges() 初始化一个空的数据流对象。
void addNum(int value) 将整数 value 添加到数据流中。
int[][] getIntervals() 返回当前数据流中的整数汇总为一组不相交的区间列表 [starti, endi]。答案应按 starti 升序排序。


```
use std::collections::BTreeSet;

#[derive(Debug, Default)]
struct SummaryRanges {
    intervals: BTreeSet<(i32, i32)>,
}

impl SummaryRanges {
    fn new() -> Self {
        Self::default()
    }

    fn add_num(&mut self, value: i32) -> &mut Self {
        // 找出所有受影响的区间（包括相邻的）
        let affected: Vec<_> = self.intervals.iter()
            .filter(|&&(s, e)| s <= value + 1 && e + 1 >= value)
            .copied()
            .collect();

        // 计算新区间边界
        let new_range = affected.iter()
            .fold((value, value), |(s, e), &(ns, ne)| (s.min(ns), e.max(ne)));

        // 移除旧区间，插入新区间
        affected.iter().for_each(|r| { self.intervals.remove(r); });
        self.intervals.insert(new_range);

        self
    }

    fn get_intervals(&self) -> Vec<Vec<i32>> {
        self.intervals.iter()
            .map(|&(s, e)| vec![s, e])
            .collect()
    }
}
```
