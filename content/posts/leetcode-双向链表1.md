---
title: "Leetcode 双向链表1"
date: 2025-06-16T22:16:50+08:00
tags: ["leetcode", "双向链表"]
draft: false
---

## LRU 缓存

请你设计并实现一个满足  LRU (最近最少使用) 缓存 约束的数据结构。
实现 LRUCache 类：
    ·LRUCache(int capacity) 以 正整数 作为容量 capacity 初始化 LRU 缓存
    ·int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
    ·void put(int key, int value) 如果关键字 key 已经存在，则变更其数据值 value ；如果不存在，则向缓存中插入该组 key-value 。如果插入操作导致关键字数量超过 capacity ，则应该 逐出 最久未使用的关键字。
函数 get 和 put 必须以 O(1) 的平均时间复杂度运行。

示例：

输入
["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
输出
[null, null, null, 1, null, -1, null, -1, 3, 4]

解释
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // 缓存是 {1=1}
lRUCache.put(2, 2); // 缓存是 {1=1, 2=2}
lRUCache.get(1);    // 返回 1
lRUCache.put(3, 3); // 该操作会使得关键字 2 作废，缓存是 {1=1, 3=3}
lRUCache.get(2);    // 返回 -1 (未找到)
lRUCache.put(4, 4); // 该操作会使得关键字 1 作废，缓存是 {4=4, 3=3}
lRUCache.get(1);    // 返回 -1 (未找到)
lRUCache.get(3);    // 返回 3
lRUCache.get(4);    // 返回 4

```
use std::collections::HashMap;
use std::cell::RefCell;
use std::rc::{Rc, Weak};

struct Node {
    key: i32,
    value: i32,
    prev: Option<Weak<RefCell<Node>>>, // 避免循环引用导致内存泄漏
    next: Option<Rc<RefCell<Node>>>,
}

impl Node {
    fn new(key: i32, value: i32) -> Rc<RefCell<Self>> {
        Rc::new(RefCell::new(Self { key, value, prev: None, next: None }))
    }
}

struct LRUCache {
    capacity: usize,
    dummy: Rc<RefCell<Node>>,
    key_to_node: HashMap<i32, Rc<RefCell<Node>>>,
}

impl LRUCache {
    fn new(capacity: i32) -> Self {
        let dummy = Node::new(0, 0);
        dummy.borrow_mut().prev = Some(Rc::downgrade(&dummy));
        dummy.borrow_mut().next = Some(dummy.clone());
        Self { capacity: capacity as usize, dummy, key_to_node: HashMap::new() }
    }

    // 获取 key 对应的节点，同时把该节点移到链表头部
    fn get_node(&mut self, key: i32) -> Option<Rc<RefCell<Node>>> {
        if let Some(node) = self.key_to_node.get(&key) { // 有这本书
            let node = node.clone();
            Self::remove(node.clone()); // 把这本书抽出来
            self.push_front(node.clone()); // 放在最上面
            return Some(node);
        }
        None // 没有这本书
    }

    fn get(&mut self, key: i32) -> i32 {
        if let Some(node) = self.get_node(key) { // 有这本书
            node.borrow().value
        } else {
            -1
        }
    }

    fn put(&mut self, key: i32, value: i32) {
        if let Some(node) = self.get_node(key) { // 有这本书
            node.borrow_mut().value = value; // 更新 value
            return;
        }
        let node = Node::new(key, value); // 新书
        self.push_front(node.clone()); // 放在最上面
        self.key_to_node.insert(key, node);
        if self.key_to_node.len() > self.capacity { // 书太多了
            let back_node = self.dummy.borrow().prev.as_ref().unwrap().upgrade().unwrap();
            self.key_to_node.remove(&back_node.borrow().key);
            Self::remove(back_node); // 去掉最后一本书
        }
    }

    // 删除一个节点（抽出一本书）
    fn remove(x: Rc<RefCell<Node>>) {
        // 断开 x 和 x.prev x.next
        let prev = x.borrow_mut().prev.take().unwrap().upgrade().unwrap();
        let next = x.borrow_mut().next.take().unwrap();
        // prev 和 next 建立连接
        next.borrow_mut().prev = Some(Rc::downgrade(&prev));
        prev.borrow_mut().next = Some(next);
    }

    // 在链表头添加一个节点（把一本书放在最上面）
    fn push_front(&mut self, x: Rc<RefCell<Node>>) {
        // 断开 dummy 和 dummy.next
        let next = self.dummy.borrow_mut().next.take().unwrap();
        // 在 dummy 和 next 之间插入 x，建立 4 条连接
        next.borrow_mut().prev = Some(Rc::downgrade(&x));
        x.borrow_mut().next = Some(next);
        x.borrow_mut().prev = Some(Rc::downgrade(&self.dummy));
        self.dummy.borrow_mut().next = Some(x);
    }
}
```