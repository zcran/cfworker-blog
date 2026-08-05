---
title: "leetcode-链表39"
date: 2026-06-29T10:55:35+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 删除链表的倒数第 N 个结点

给定一个链表，删除链表的倒数第 n 个结点，并且返回链表的头结点。




```
impl Solution {
    pub fn remove_nth_from_end(head: Option<Box<ListNode>>, n: i32) -> Option<Box<ListNode>> {
        // 使用哨兵节点简化边界处理
        let mut dummy = Box::new(ListNode::new(0));
        dummy.next = head;

        // 先获取链表长度
        let mut len = 0;
        let mut cur = dummy.next.as_ref();
        while let Some(node) = cur {
            len += 1;
            cur = node.next.as_ref();
        }

        // 计算要删除节点的前一个位置（从 0 开始）
        let target = len - n;

        // 移动 slow 到待删除节点的前一个节点
        let mut slow = &mut dummy;
        for _ in 0..target {
            slow = slow.next.as_mut().unwrap();
        }

        // 删除目标节点
        let to_delete = slow.next.take();
        slow.next = to_delete.and_then(|node| node.next);

        dummy.next
    }
}
```
