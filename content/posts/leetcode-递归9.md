---
title: "leetcode-递归9"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 移除链表元素


给你一个链表的头节点 head 和一个整数 val ，请你删除链表中所有满足 Node.val == val 的节点，并返回 新的头节点 。

```
impl Solution {
    pub fn remove_elements(head: Option<Box<ListNode>>, val: i32) -> Option<Box<ListNode>> {
        let mut root = ListNode { val: 0, next: head };
        let mut head = &mut root;
        while let Some(ref mut node) = head.next {
            if node.val == val {
                head.next = node.next.take();
            } else {
                head = head.next.as_mut()?;
            }
        }
        root.next
    }
}

```
