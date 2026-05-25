---
title: "leetcode-队列6"
date: 2026-05-23T09:21:37+08:00
tags: ["leetcode", "队列"]
draft: false
---


## 设计循环双端队列



设计实现双端队列。

实现 MyCircularDeque 类:

MyCircularDeque(int k) ：构造函数,双端队列最大为 k 。
boolean insertFront()：将一个元素添加到双端队列头部。 如果操作成功返回 true ，否则返回 false 。
boolean insertLast() ：将一个元素添加到双端队列尾部。如果操作成功返回 true ，否则返回 false 。
boolean deleteFront() ：从双端队列头部删除一个元素。 如果操作成功返回 true ，否则返回 false 。
boolean deleteLast() ：从双端队列尾部删除一个元素。如果操作成功返回 true ，否则返回 false 。
int getFront() )：从双端队列头部获得一个元素。如果双端队列为空，返回 -1 。
int getRear() ：获得双端队列的最后一个元素。 如果双端队列为空，返回 -1 。
boolean isEmpty() ：若双端队列为空，则返回 true ，否则返回 false  。
boolean isFull() ：若双端队列满了，则返回 true ，否则返回 false 。


```
pub struct MyCircularDeque {
    arr: Vec<i32>,   // 底层数组
    head: usize,     // 队首元素索引（有效时）
    tail: usize,     // 队尾元素索引（有效时）
    count: usize,    // 当前元素个数
    cap: usize,      // 容量
}

impl MyCircularDeque {
    /// 初始化容量为 k 的双端循环队列
    pub fn new(k: i32) -> Self {
        let cap = k as usize;
        MyCircularDeque {
            arr: vec![0; cap],
            head: 0,
            tail: 0,
            count: 0,
            cap,
        }
    }

    /// 在队首插入元素，成功返回 true，队列满返回 false
    pub fn insert_front(&mut self, value: i32) -> bool {
        if self.is_full() {
            return false;
        }
        if self.is_empty() {
            self.head = 0;
            self.tail = 0;
        } else {
            self.head = (self.head + self.cap - 1) % self.cap;
        }
        self.arr[self.head] = value;
        self.count += 1;
        true
    }

    /// 在队尾插入元素，成功返回 true，队列满返回 false
    pub fn insert_last(&mut self, value: i32) -> bool {
        if self.is_full() {
            return false;
        }
        if self.is_empty() {
            self.head = 0;
            self.tail = 0;
        } else {
            self.tail = (self.tail + 1) % self.cap;
        }
        self.arr[self.tail] = value;
        self.count += 1;
        true
    }

    /// 删除队首元素，成功返回 true，队列空返回 false
    pub fn delete_front(&mut self) -> bool {
        if self.is_empty() {
            return false;
        }
        if self.count > 1 {
            self.head = (self.head + 1) % self.cap;
        }
        self.count -= 1;
        true
    }

    /// 删除队尾元素，成功返回 true，队列空返回 false
    pub fn delete_last(&mut self) -> bool {
        if self.is_empty() {
            return false;
        }
        if self.count > 1 {
            self.tail = (self.tail + self.cap - 1) % self.cap;
        }
        self.count -= 1;
        true
    }

    /// 获取队首元素，队列空返回 -1
    pub fn get_front(&self) -> i32 {
        if self.is_empty() { -1 } else { self.arr[self.head] }
    }

    /// 获取队尾元素，队列空返回 -1
    pub fn get_rear(&self) -> i32 {
        if self.is_empty() { -1 } else { self.arr[self.tail] }
    }

    pub fn is_empty(&self) -> bool {
        self.count == 0
    }

    pub fn is_full(&self) -> bool {
        self.count == self.cap
    }
}
```
