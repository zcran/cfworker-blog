---
title: "Rust Note 26"
date: 2023-06-04T21:16:32+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 环形链表

给你一个链表的头节点 head ，判断链表中是否有环。

如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。注意：pos 不作为参数进行传递 。仅仅是为了标识链表的实际情况。

如果链表中存在环 ，则返回 true 。 否则，返回 false 。

### C 解法：
```
 // 快慢指针，floyd判圈算法
class Solution {
public:
    bool hasCycle(ListNode* head) {
        if (head == nullptr || head->next == nullptr) {
            return false;
        }
        ListNode* slow = head;
        ListNode* fast = head->next;
        while (slow != fast) {
            if (fast == nullptr || fast->next == nullptr) {
                return false;
            }
            slow = slow->next;
            fast = fast->next->next;
        }
        return true;
    }
};
```


### Rust 解法1：
```
 // hash set
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::HashSet;

struct ListNode{
    val: i32,
    next: Option<Rc<RefCell<ListNode>>>,
}

impl ListNode{
    pub fn new(val: i32) -> ListNode {
        ListNode { 
            val, 
            next: None,
        }
    }
}

pub fn has_cycle(head: Option<Rc<RefCell<ListNode>>>) -> bool {
    let mut set = HashSet::new();

    let mut h = match head {
        Some(ref node) => Rc::clone(node),
        _ => return false,
    };

    loop {
        if set.contains(&Rc::as_ptr(&h)) { return true; }
        set.insert(Rc::as_ptr(&h));
        let next = match h.borrow().next {
            Some(ref node) => Rc::clone(node),
            _ => break,
        };
        h = next;
    }

    false
}
```

### Rust解法2:
```
// 破坏原链表结构
pub fn has_cycle_ii(mut head: Option<Rc<RefCell<ListNode>>>) -> bool {
    let mut set = std::collections::HashSet::new();

    while let Some(node) = head {
        let p = Rc::as_ptr(&node);
        if set.contains(&p) { return true; }
        set.insert(p);
        head = node.borrow_mut().next.take();
    }

    false
}
```

### Rust解法3:
```
pub fn has_cycle_ex(head: Option<Rc<RefCell<ListNode>>>) -> bool {
    let mut turtle = match head {
        Some(ref node) => Rc::clone(node),
        None => return false,
    };
    let mut rabbit = Rc::clone(&turtle);
    loop {
        if turtle.borrow().next.is_none() { return false; }
        let t = Rc::clone(turtle.borrow().next.as_ref().unwrap());
        turtle = t;

        for _ in 0..2 {
            if rabbit.borrow().next.is_none() { return false; }
            let r = Rc::clone(rabbit.borrow().next.as_ref().unwrap());
            rabbit = r;
        }

        if rabbit.as_ptr() == turtle.as_ptr() { return true; }
    }
}
```