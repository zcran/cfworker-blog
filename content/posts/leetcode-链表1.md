---
title: "leetcode-链表1"
date: 2026-06-29T10:55:33+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 删除链表的倒数第 N 个结点

给你一个链表，删除链表的倒数第 n 个结点，并且返回链表的头结点。


```
impl Solution {
    pub fn remove_nth_from_end(head: Option<Box<ListNode>>, n: i32) -> Option<Box<ListNode>> {
        // 哑节点简化头节点删除逻辑
        let mut dummy = Box::new(ListNode { val: 0, next: head });

        // 快指针先走 n 步
        let mut fast = dummy.clone();
        for _ in 0..n {
            fast = fast.next.unwrap();
        }

        // 慢指针指向哑节点，快慢指针同步移动
        let mut slow = &mut dummy;
        while fast.next.is_some() {
            slow = slow.next.as_mut().unwrap();
            fast = fast.next.unwrap();
        }

        // 删除慢指针的下一个节点（倒数第 n 个）
        slow.next = slow.next.take().unwrap().next;

        dummy.next
    }
}
```
