---
title: "leetcode-字符串56"
date: 2026-10-10T12:56:56+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 判断两个事件是否存在冲突

给你两个字符串数组 event1 和 event2 ，表示发生在同一天的两个闭区间时间段事件，其中：

event1 = [startTime1, endTime1] 且
event2 = [startTime2, endTime2]

事件的时间为有效的 24 小时制且按 HH:MM 格式给出。

当两个事件存在某个非空的交集时（即，某些时刻是两个事件都包含的），则认为出现 冲突 。

如果两个事件之间存在冲突，返回 true ；否则，返回 false 。


```
impl Solution {
    pub fn have_conflict(event1: Vec<String>, event2: Vec<String>) -> bool {
        // "HH:MM" 字典序与时间顺序一致，可直接比较字符串
        event1[0] <= event2[1] && event2[0] <= event1[1]
    }
}
```
