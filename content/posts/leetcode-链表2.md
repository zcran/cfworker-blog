---
title: "leetcode-链表2"
date: 2026-06-29T10:55:34+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 旋转链表

给你一个链表的头节点 head ，旋转链表，将链表每个节点向右移动 k 个位置。


```
impl Solution {
    pub fn rotate_right(head: Option<Box<ListNode>>, k: i32) -> Option<Box<ListNode>> {
        // 处理空链表或无需旋转的情况
        if head.is_none() || k == 0 {
            return head;
        }

        // 1. 收集所有节点值到 Vec，同时计算长度
        let mut vals = Vec::new();
        let mut cur = &head;
        while let Some(node) = cur {
            vals.push(node.val);
            cur = &node.next;
        }
        let len = vals.len();
        let k = (k as usize) % len;

        // 若旋转后顺序不变则直接返回原链表
        if k == 0 {
            return head;
        }

        // 2. 原地旋转 Vec（右移 k 位）
        vals.rotate_right(k);

        // 3. 根据旋转后的值重建链表
        let mut dummy = Box::new(ListNode { val: 0, next: None });
        let mut tail = &mut dummy;
        for val in vals {
            tail.next = Some(Box::new(ListNode { val, next: None }));
            tail = tail.next.as_mut().unwrap();
        }
        dummy.next
    }
}
```
