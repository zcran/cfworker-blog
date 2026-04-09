---
title: "leetcode-单调队列23"
date: 2026-03-31T20:55:29+08:00
tags: ["leetcode", "单调队列"]
draft: false
---


## 设计自助结算系统

请设计一个自助结账系统，该系统需要通过一个队列来模拟顾客通过购物车的结算过程，需要实现的功能有：

get_max()：获取结算商品中的最高价格，如果队列为空，则返回 -1
add(value)：将价格为 value 的商品加入待结算商品队列的尾部
remove()：移除第一个待结算的商品价格，如果队列为空，则返回 -1
注意，为保证该系统运转高效性，以上函数的均摊时间复杂度均为 O(1)

```
use std::collections::VecDeque;

struct Checkout {
    queue: VecDeque<i32>,
    max_deque: VecDeque<usize>,
    offset: usize,  // 记录已经移除的元素数量
}

impl Checkout {
    fn new() -> Self {
        Self {
            queue: VecDeque::new(),
            max_deque: VecDeque::new(),
            offset: 0,
        }
    }

    fn get_max(&self) -> i32 {
        if self.queue.is_empty() {
            return -1;
        }
        self.queue[*self.max_deque.front().unwrap() - self.offset]
    }

    fn add(&mut self, value: i32) {
        self.queue.push_back(value);
        let real_idx = self.offset + self.queue.len() - 1;

        while let Some(&back_idx) = self.max_deque.back() {
            if self.queue[back_idx - self.offset] <= value {
                self.max_deque.pop_back();
            } else {
                break;
            }
        }
        self.max_deque.push_back(real_idx);
    }

    fn remove(&mut self) -> i32 {
        if self.queue.is_empty() {
            return -1;
        }

        let removed = self.queue.pop_front().unwrap();
        self.offset += 1;

        if let Some(&front_idx) = self.max_deque.front() {
            if front_idx < self.offset {
                self.max_deque.pop_front();
            }
        }

        removed
    }
}
```
