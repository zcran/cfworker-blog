---
title: "leetcode-链表43"
date: 2026-06-29T10:55:35+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 合并 K 个升序链表

给定一个链表数组，每个链表都已经按升序排列。

请将所有链表合并到一个升序链表中，返回合并后的链表。


```
use std::cmp::Ordering;

impl Solution {
    pub fn merge_k_lists(lists: Vec<Option<Box<ListNode>>>) -> Option<Box<ListNode>> {
        if lists.is_empty() {
            return None;
        }
        Self::merge_range(&lists, 0, lists.len())
    }

    // 分治合并指定范围的链表
    fn merge_range(lists: &[Option<Box<ListNode>>], left: usize, right: usize) -> Option<Box<ListNode>> {
        if left + 1 == right {
            return lists[left].clone(); // 单个链表直接返回
        }

        let mid = (left + right) >> 1;
        let l1 = Self::merge_range(lists, left, mid);
        let l2 = Self::merge_range(lists, mid, right);
        Self::merge_two(l1, l2)
    }

    // 合并两个有序链表（迭代版本，更高效）
    fn merge_two(l1: Option<Box<ListNode>>, l2: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        let mut dummy = Box::new(ListNode::new(0));
        let mut tail = &mut dummy;
        let mut left = l1;
        let mut right = l2;

        while let (Some(l), Some(r)) = (left.as_ref(), right.as_ref()) {
            if l.val <= r.val {
                let next = left.as_mut().unwrap().next.take();
                tail.next = left;
                left = next;
            } else {
                let next = right.as_mut().unwrap().next.take();
                tail.next = right;
                right = next;
            }
            tail = tail.next.as_mut().unwrap();
        }

        // 连接剩余部分
        tail.next = if left.is_some() { left } else { right };
        dummy.next
    }
}
```
