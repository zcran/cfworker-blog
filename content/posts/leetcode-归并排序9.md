---
title: "Leetcode 归并排序9"
date: 2025-05-23T21:22:00+08:00
tags: ["leetcode", "归并排序"]
draft: false
---

## 排序链表

给定链表的头结点 head ，请将其按 升序 排列并返回 排序后的链表 。

```
impl Solution {
    pub fn sort_list(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        let mut size = 0;
        let mut cur = head.as_ref();
        
        while let Some(node) = cur {
            cur = node.next.as_ref();
            size += 1;
        }

        Self::split_list(head, size)
    }

    fn split_list(mut head: Option<Box<ListNode>>, size: i32) -> Option<Box<ListNode>> {
        if size <= 1 { return head; }

        let half_size = size / 2;
        let mut cur = &mut head;

        for _ in 0..half_size {
            cur = &mut cur.as_mut().unwrap().next;
        }

        let next_list = cur.take();
        let l1 = Self::split_list(head, half_size);
        let l2 = Self::split_list(next_list, size - half_size);

        Self::merge_list(l1, l2)
    }

    fn merge_list(l1: Option<Box<ListNode>>, l2: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        match (l1, l2) {
            (None, None) => None,
            (Some(l1), None) => Some(l1),
            (None, Some(l2)) => Some(l2),
            (Some(mut l1), Some(mut l2)) => {
                if l1.val < l2.val {
                    l1.next = Self::merge_list(l1.next, Some(l2));
                    Some(l1)
                } else {
                    l2.next = Self::merge_list(Some(l1), l2.next);
                    Some(l2)
                }
            }
        }
    }
}
```