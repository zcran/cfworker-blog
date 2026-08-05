---
title: "leetcode-链表6"
date: 2026-06-29T10:55:34+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 反转链表 II


给你单链表的头指针 head 和两个整数 left 和 right ，其中 left <= right 。请你反转从位置 left 到位置 right 的链表节点，返回 反转后的链表 。

```
// Definition for singly-linked list.
// #[derive(PartialEq, Eq, Clone, Debug)]
// pub struct ListNode {
//   pub val: i32,
//   pub next: Option<Box<ListNode>>
// }
//
// impl ListNode {
//   #[inline]
//   fn new(val: i32) -> Self {
//     ListNode {
//       next: None,
//       val
//     }
//   }
// }
impl Solution {
    pub fn reverse_between(head: Option<Box<ListNode>>, left: i32, right: i32) -> Option<Box<ListNode>> {
        let mut cur = head;
        let mut v = Vec::new();
        while let Some(node) = cur {
            v.push(node.val);
            cur = node.next;
        }
        let times = (right - left + 1) / 2;
        for i in 0..(times as i32) {
            v.swap((left+i-1) as usize, (right-i-1) as usize);
        }
        v.reverse();
        let mut prev = None;
        for i in 0..v.len() {
            let last = v[i];
            let mut node = ListNode::new(last);
            node.next = prev;
            prev = Some(Box::new(node));
        }
        prev
    }
}
```
