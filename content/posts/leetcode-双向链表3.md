---
title: "Leetcode 双向链表3"
date: 2025-06-16T22:16:50+08:00
tags: ["leetcode", "双向链表"]
draft: false
---

## 全O(1)的数据结构


请你设计一个用于存储字符串计数的数据结构，并能够返回计数最小和最大的字符串。

实现 AllOne 类：

AllOne() 初始化数据结构的对象。
inc(String key) 字符串 key 的计数增加 1 。如果数据结构中尚不存在 key ，那么插入计数为 1 的 key 。
dec(String key) 字符串 key 的计数减少 1 。如果 key 的计数在减少后为 0 ，那么需要将这个 key 从数据结构中删除。测试用例保证：在减少计数前，key 存在于数据结构中。
getMaxKey() 返回任意一个计数最大的字符串。如果没有元素存在，返回一个空字符串 "" 。
getMinKey() 返回任意一个计数最小的字符串。如果没有元素存在，返回一个空字符串 "" 。
注意：每个函数都应当满足 O(1) 平均时间复杂度。


```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::HashMap;

#[derive(Debug, PartialEq, Clone, Eq)]
struct Node {
    key: String,
    count: i32,
    prev: Option<Rc<RefCell<Node>>>,
    next: Option<Rc<RefCell<Node>>>,
}
impl Node {
    fn new(key: String) -> Self {
        Self {
            key,
            count: 1,
            prev: None,
            next: None,
        }
    }
}

struct DoubleList {
    head: Rc<RefCell<Node>>,
    tail: Rc<RefCell<Node>>,
}
impl DoubleList {
    fn new() -> Self {
        let mut head = Rc::new(RefCell::new(Node::new("".to_string())));
        let mut tail = Rc::new(RefCell::new(Node::new("".to_string())));
        head.borrow_mut().next = Some(tail.clone());
        tail.borrow_mut().prev = Some(head.clone());
        Self {
            head,
            tail,
        }
    }

    fn get_max_key(&self) -> String {
        let next = self.head.borrow().next.clone().unwrap();
        if next == self.tail {
            "".to_string()
        } else {
            next.borrow().key.clone()
        }
    }
    fn get_min_key(&self) -> String {
        let prev = self.tail.borrow().prev.clone().unwrap();
        if prev == self.head {
            "".to_string()
        } else {
            prev.borrow().key.clone()
        }
    }

    fn insert_tail(&mut self, node: Rc<RefCell<Node>>) {
        let prev = self.tail.borrow_mut().prev.take().unwrap();
        prev.borrow_mut().next = Some(node.clone());
        self.tail.borrow_mut().prev = Some(node.clone());
        node.borrow_mut().prev = Some(prev);
        node.borrow_mut().next = Some(self.tail.clone());
    }

    fn remove(&mut self, node: Rc<RefCell<Node>>) {
        let prev = node.borrow_mut().prev.take().unwrap();
        let next = node.borrow_mut().next.take().unwrap();
        prev.borrow_mut().next = Some(next.clone());
        next.borrow_mut().prev = Some(prev);
    }
    fn insert(&mut self, node: Rc<RefCell<Node>>) {
        let count = node.borrow().count;
        let mut cur = self.head.borrow().next.clone().unwrap();
        while cur != self.tail && cur.borrow().count > count {
            let next = cur.borrow().next.clone().unwrap();
            cur = next;
        }

        let prev = cur.borrow_mut().prev.take().unwrap();
        prev.borrow_mut().next = Some(node.clone());
        cur.borrow_mut().prev = Some(node.clone());
        node.borrow_mut().prev = Some(prev);
        node.borrow_mut().next = Some(cur);
    }
    fn rearrange(&mut self, node: Rc<RefCell<Node>>) {
        self.remove(node.clone());
        self.insert(node);
    }
}

struct AllOne {
    maps: HashMap<String, Rc<RefCell<Node>>>,
    list: DoubleList,
}

/**
 * `&self` means the method takes an immutable reference.
 * If you need a mutable reference, change it to `&mut self` instead.
 */
impl AllOne {
    fn new() -> Self {
        Self {
            maps: HashMap::new(),
            list: DoubleList::new(),
        }
    }

    fn inc(&mut self, key: String) {
        match self.maps.get_mut(&key) {
            Some(node) => {
                node.borrow_mut().count += 1;
                self.list.rearrange(node.clone());
            }
            None => {
                let node = Rc::new(RefCell::new(Node::new(key.clone())));
                self.list.insert_tail(node.clone());
                self.maps.insert(key, node);
            }
        }
    }

    fn dec(&mut self, key: String) {
        let node = self.maps.get_mut(&key).unwrap();
        node.borrow_mut().count -= 1;
        if node.borrow().count == 0 {
            self.list.remove(self.maps.remove(&key).unwrap());
        } else {
            self.list.rearrange(node.clone());
        }
    }

    fn get_max_key(&self) -> String {
        self.list.get_max_key()
    }
    fn get_min_key(&self) -> String {
        self.list.get_min_key()
    }
}

/**
 * Your AllOne object will be instantiated and called as such:
 * let obj = AllOne::new();
 * obj.inc(key);
 * obj.dec(key);
 * let ret_3: String = obj.get_max_key();
 * let ret_4: String = obj.get_min_key();
 */

```
