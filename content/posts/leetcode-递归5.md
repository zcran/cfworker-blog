---
title: "leetcode-递归5"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


## K 个一组翻转链表

给你链表的头节点 head ，每 k 个节点一组进行翻转，请你返回修改后的链表。

k 是一个正整数，它的值小于或等于链表的长度。如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。

你不能只是单纯的改变节点内部的值，而是需要实际进行节点交换。

```
impl Solution {
    /// K 个一组翻转链表（性能优化版）
    pub fn reverse_k_group(mut head: Option<Box<ListNode>>, k: i32) -> Option<Box<ListNode>> {
        let k = k as usize;

        // 快速路径：空链表或单节点
        if head.is_none() || k <= 1 {
            return head;
        }

        // 计算链表长度
        let mut len = 0;
        let mut curr = head.as_ref();
        while let Some(node) = curr {
            len += 1;
            curr = node.next.as_ref();
        }

        if len < k {
            return head;
        }

        // 递归翻转
        let mut prev = None;
        let mut curr = head;

        for _ in 0..k {
            let mut node = curr.take().unwrap();
            curr = node.next.take();
            node.next = prev;
            prev = Some(node);
        }

        // 连接剩余部分
        let mut tail = prev.as_mut().unwrap();
        while tail.next.is_some() {
            tail = tail.next.as_mut().unwrap();
        }
        tail.next = Self::reverse_k_group(curr, k as i32);

        prev
    }
}
```
