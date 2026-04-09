---
title: "leetcode-数据流16"
date: 2026-04-06T20:57:54+08:00
tags: ["leetcode", "数据流"]
draft: false
---


## 最近的请求次数

写一个 RecentCounter 类来计算特定时间范围内最近的请求。

请实现 RecentCounter 类：

RecentCounter() 初始化计数器，请求数为 0 。
int ping(int t) 在时间 t 添加一个新请求，其中 t 表示以毫秒为单位的某个时间，并返回过去 3000 毫秒内发生的所有请求数（包括新请求）。确切地说，返回在 [t-3000, t] 内发生的请求数。
保证 每次对 ping 的调用都使用比之前更大的 t 值。


```
use std::collections::VecDeque;

#[derive(Default)]
struct RecentCounter {
    counter: VecDeque<i32>,
}

impl RecentCounter {
    fn new() -> Self {
        Self::default()
    }

    fn ping(&mut self, t: i32) -> i32 {
        self.counter.push_back(t);
        let threshold = t - 3000;

        // 移除所有过期的请求
        while let Some(&front) = self.counter.front() {
            if front < threshold {
                self.counter.pop_front();
            } else {
                break;
            }
        }

        self.counter.len() as i32
    }
}

```
