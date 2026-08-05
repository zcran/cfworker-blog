---
title: "leetcode-链表42"
date: 2026-06-29T10:55:35+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 排序链表


给定链表的头结点 head ，请将其按 升序 排列并返回 排序后的链表 。

```
impl Solution {
    pub fn sort_list(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        // 基本情况：空链表或单节点链表已有序
        if head.is_none() || head.as_ref().unwrap().next.is_none() {
            return head;
        }

        // 计算链表长度
        let mut len = 0;
        let mut cur = head.as_ref();
        while let Some(node) = cur {
            len += 1;
            cur = node.next.as_ref();
        }

        // 分割链表
        let mut head = head;
        let mut cur = &mut head;
        for _ in 0..len / 2 - 1 {
            cur = &mut cur.as_mut().unwrap().next;
        }
        let right = cur.as_mut().unwrap().next.take();

        // 递归排序左右两部分
        let left_sorted = Self::sort_list(head);
        let right_sorted = Self::sort_list(right);

        // 合并两个有序链表
        Self::merge(left_sorted, right_sorted)
    }

    // 合并两个有序链表
    fn merge(l1: Option<Box<ListNode>>, l2: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
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
