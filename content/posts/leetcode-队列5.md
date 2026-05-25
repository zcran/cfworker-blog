---
title: "leetcode-队列5"
date: 2026-05-23T09:21:37+08:00
tags: ["leetcode", "队列"]
draft: false
---


## 设计循环队列

设计你的循环队列实现。 循环队列是一种线性数据结构，其操作表现基于 FIFO（先进先出）原则并且队尾被连接在队首之后以形成一个循环。它也被称为“环形缓冲器”。

循环队列的一个好处是我们可以利用这个队列之前用过的空间。在一个普通队列里，一旦一个队列满了，我们就不能插入下一个元素，即使在队列前面仍有空间。但是使用循环队列，我们能使用这些空间去存储新的值。

你的实现应该支持如下操作：

MyCircularQueue(k): 构造器，设置队列长度为 k 。
Front: 从队首获取元素。如果队列为空，返回 -1 。
Rear: 获取队尾元素。如果队列为空，返回 -1 。
enQueue(value): 向循环队列插入一个元素。如果成功插入则返回真。
deQueue(): 从循环队列中删除一个元素。如果成功删除则返回真。
isEmpty(): 检查循环队列是否为空。
isFull(): 检查循环队列是否已满。



```
pub struct MyCircularQueue {
    data: Vec<i32>,   // 底层数组
    head: usize,      // 队首指针（指向第一个元素）
    tail: usize,      // 队尾指针（指向下一个插入位置）
    count: usize,     // 当前元素数量
    cap: usize,       // 容量（data.len()）
}

impl MyCircularQueue {
    /// 初始化容量为 k 的循环队列
    pub fn new(k: i32) -> Self {
        let cap = k as usize;
        MyCircularQueue {
            data: vec![0; cap],
            head: 0,
            tail: 0,
            count: 0,
            cap,
        }
    }

    /// 向队尾插入元素，成功返回 true，队列满返回 false
    pub fn en_queue(&mut self, value: i32) -> bool {
        if self.is_full() {
            return false;
        }
        self.data[self.tail] = value;
        self.tail = (self.tail + 1) % self.cap;
        self.count += 1;
        true
    }

    /// 从队首删除元素，成功返回 true，队列空返回 false
    pub fn de_queue(&mut self) -> bool {
        if self.is_empty() {
            return false;
        }
        self.head = (self.head + 1) % self.cap;
        self.count -= 1;
        true
    }

    /// 获取队首元素，队列空返回 -1
    pub fn front(&self) -> i32 {
        if self.is_empty() {
            return -1;
        }
        self.data[self.head]
    }

    /// 获取队尾元素，队列空返回 -1
    pub fn rear(&self) -> i32 {
        if self.is_empty() {
            return -1;
        }
        // 避免模运算，使用 if 判断提升可读性
        let rear_idx = if self.tail == 0 {
            self.cap - 1
        } else {
            self.tail - 1
        };
        self.data[rear_idx]
    }

    /// 判断队列是否为空
    pub fn is_empty(&self) -> bool {
        self.count == 0
    }

    /// 判断队列是否已满
    pub fn is_full(&self) -> bool {
        self.count == self.cap
    }
}
```
