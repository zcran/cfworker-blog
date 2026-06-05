---
title: "leetcode-递归14"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 回文链表


给你一个单链表的头节点 head ，请你判断该链表是否为回文链表。如果是，返回 true ；否则，返回 false 。


```
impl Solution {
    pub fn is_palindrome(mut head: Option<Box<ListNode>>) -> bool {
        let mut vec = Vec::new();
        while let Some(boxed_node) = head {
            let node = *boxed_node;
            vec.push(node.val);
            head = node.next;
        }
        let mut reserved_vec = vec.clone();
        reserved_vec.reverse();
        vec == reserved_vec
    }
}
```
