---
title: "leetcode-链表13"
date: 2026-06-29T10:55:34+08:00
tags: ["leetcode", "链表"]
draft: false
---


## LRU 缓存

请你设计并实现一个满足  LRU (最近最少使用) 缓存 约束的数据结构。
实现 LRUCache 类：

· LRUCache(int capacity) 以 正整数 作为容量 capacity 初始化 LRU 缓存

· int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。

· void put(int key, int value) 如果关键字 key 已经存在，则变更其数据值 value ；如果不存在，则向缓存中插入该组 key-value 。如果插入操作导致关键字数量超过 capacity ，则应该 逐出 最久未使用的关键字。

函数 get 和 put 必须以 O(1) 的平均时间复杂度运行。


```
use std::collections::HashMap;
use std::cell::RefCell;
use std::rc::{Rc, Weak};

/// 双向链表节点
struct Node {
    key: i32,
    value: i32,
    prev: Option<Weak<RefCell<Node>>>,
    next: Option<Rc<RefCell<Node>>>,
}

impl Node {
    fn new(key: i32, value: i32) -> Self {
        Node {
            key,
            value,
            prev: None,
            next: None,
        }
    }

    fn new_rc(key: i32, value: i32) -> Rc<RefCell<Self>> {
        Rc::new(RefCell::new(Node::new(key, value)))
    }
}

/// LRU 缓存
///
/// 使用哈希表 + 双向链表实现 O(1) 的 get 和 put 操作
/// - 哈希表：快速定位节点
/// - 双向链表：维护访问顺序，头部为最近使用，尾部为最久未使用
pub struct LRUCache {
    capacity: usize,
    cache: HashMap<i32, Rc<RefCell<Node>>>,
    head: Rc<RefCell<Node>>, // 哨兵头节点
    tail: Rc<RefCell<Node>>, // 哨兵尾节点
}

impl LRUCache {
    /// 初始化 LRU 缓存
    pub fn new(capacity: i32) -> Self {
        let head = Rc::new(RefCell::new(Node::new(0, 0)));
        let tail = Rc::new(RefCell::new(Node::new(0, 0)));

        // 连接哨兵节点
        head.borrow_mut().next = Some(tail.clone());
        tail.borrow_mut().prev = Some(Rc::downgrade(&head));

        LRUCache {
            capacity: capacity as usize,
            cache: HashMap::new(),
            head,
            tail,
        }
    }

    /// 获取缓存值，如果不存在返回 -1
    pub fn get(&mut self, key: i32) -> i32 {
        if let Some(node) = self.cache.get(&key) {
            let node = node.clone();
            let value = node.borrow().value;
            self.move_to_head(&node);
            value
        } else {
            -1
        }
    }

    /// 插入或更新缓存
    pub fn put(&mut self, key: i32, value: i32) {
        if let Some(node) = self.cache.get(&key) {
            // key 已存在，更新值并移动到头部
            let node = node.clone();
            node.borrow_mut().value = value;
            self.move_to_head(&node);
            return;
        }

        // key 不存在，创建新节点
        let new_node = Node::new_rc(key, value);
        self.cache.insert(key, new_node.clone());
        self.add_to_head(&new_node);

        // 超出容量，移除尾部节点
        if self.cache.len() > self.capacity {
            // 提前获取尾部节点的 key，避免借用冲突
            let tail_key = {
                let tail_borrow = self.tail.borrow();
                if let Some(prev) = tail_borrow.prev.as_ref() {
                    if let Some(tail_node) = prev.upgrade() {
                        Some(tail_node.borrow().key)
                    } else {
                        None
                    }
                } else {
                    None
                }
            };

            if let Some(key_to_remove) = tail_key {
                // 从哈希表中移除
                if let Some(node_to_remove) = self.cache.remove(&key_to_remove) {
                    // 从链表中移除节点
                    self.remove_node(&node_to_remove);
                }
            }
        }
    }

    /// 将节点移动到头部（最近使用）
    fn move_to_head(&mut self, node: &Rc<RefCell<Node>>) {
        self.remove_node(node);
        self.add_to_head(node);
    }

    /// 从链表中移除节点
    fn remove_node(&mut self, node: &Rc<RefCell<Node>>) {
        let prev = node.borrow().prev.as_ref().unwrap().upgrade().unwrap();
        let next = node.borrow().next.as_ref().unwrap().clone();

        prev.borrow_mut().next = Some(next.clone());
        next.borrow_mut().prev = Some(Rc::downgrade(&prev));
    }

    /// 将节点添加到头部
    fn add_to_head(&mut self, node: &Rc<RefCell<Node>>) {
        let head_next = self.head.borrow().next.as_ref().unwrap().clone();

        // 连接 node 和 head_next
        node.borrow_mut().next = Some(head_next.clone());
        head_next.borrow_mut().prev = Some(Rc::downgrade(node));

        // 连接 head 和 node
        node.borrow_mut().prev = Some(Rc::downgrade(&self.head));
        self.head.borrow_mut().next = Some(node.clone());
    }
}
```
