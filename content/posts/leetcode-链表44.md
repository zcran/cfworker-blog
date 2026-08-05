---
title: "leetcode-链表44"
date: 2026-06-29T10:55:36+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 删除链表的节点


给定单向链表的头指针和一个要删除的节点的值，定义一个函数删除该节点。

返回删除后的链表的头节点。

```
impl Solution {
    pub fn delete_node(head: Option<Box<ListNode>>, val: i32) -> Option<Box<ListNode>> {
        // 使用哨兵节点简化删除操作，包括头节点删除
        let mut dummy = Box::new(ListNode::new(0));
        dummy.next = head;

        let mut current = &mut dummy;
        while let Some(ref mut node) = current.next {
            if node.val == val {
                // 找到目标节点，跳过它
                current.next = node.next.take();
                break; // 题目只要求删除第一个匹配的节点
            }
            current = current.next.as_mut().unwrap();
        }

        dummy.next
    }
}
```
