---
title: "leetcode-数据流15"
date: 2026-04-06T20:57:54+08:00
tags: ["leetcode", "数据流"]
draft: false
---


## 数据流中的移动平均值

给定一个整数数据流和一个窗口大小，根据该滑动窗口的大小，计算滑动窗口里所有数字的平均值。

实现 MovingAverage 类：

MovingAverage(int size) 用窗口大小 size 初始化对象。
double next(int val) 成员函数 next 每次调用的时候都会往滑动窗口增加一个整数，请计算并返回数据流中最后 size 个值的移动平均值，即滑动窗口里所有数字的平均值。

```
use std::collections::VecDeque;

struct MovingAverage {
    k: i32,
    deque: VecDeque<i32>,
}

/**
 * `&self` means the method takes an immutable reference.
 * If you need a mutable reference, change it to `&mut self` instead.
 */
impl MovingAverage {
    /** Initialize your data structure here. */
    fn new(size: i32) -> Self {
        MovingAverage {
            k: size,
            deque: VecDeque::new(),
        }
    }
    // 1 2 3 4 5 -> 3
    // 1
    // 1 3
    // 1 3 6
    fn next(&mut self, val: i32) -> f64 {
        self.deque.push_back(val);
        if self.deque.len() > self.k as usize {
            self.deque.pop_front();
        }
        let mut ans: f64 = 0.0;
        for num in self.deque.iter() {
            ans += *num as f64;
        }
        return ans / (self.deque.len() as f64);
    }

}
```
