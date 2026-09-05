---
title: "leetcode-堆46"
date: 2026-08-21T09:54:13+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 装满杯子需要的最短总时长

现有一台饮水机，可以制备冷水、温水和热水。每秒钟，可以装满 2 杯 不同 类型的水或者 1 杯任意类型的水。

给你一个下标从 0 开始、长度为 3 的整数数组 amount ，其中 amount[0]、amount[1] 和 amount[2] 分别表示需要装满冷水、温水和热水的杯子数量。返回装满所有杯子所需的 最少 秒数。


```
impl Solution {
    pub fn fill_cups(mut amount: Vec<i32>) -> i32 {
        // 排序后取最大值为 m，同时计算总和 s
        amount.sort_unstable();
        let m = amount[2];
        let s = amount[0] + amount[1] + m;
        m.max((s + 1) / 2)
    }
}
```
