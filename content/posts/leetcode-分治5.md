---
title: "leetcode-分治5"
date: 2026-06-13T10:51:38+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 排序链表

给你链表的头结点 head ，请将其按 升序 排列并返回 排序后的链表 。

```
impl Solution {
    /// 对链表进行排序（基于值排序）
    ///
    /// 算法：将链表转换为 Vec，排序后再重构链表
    ///
    /// 时间复杂度：O(n log n)，主要由排序决定
    /// 空间复杂度：O(n)，需要存储所有节点
    pub fn sort_list(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        // 收集所有节点的值
        let mut vals = Vec::new();
        let mut curr = head;

        while let Some(node) = curr {
            vals.push(node.val);
            curr = node.next;
        }

        // 排序
        vals.sort_unstable();

        // 重构链表
        let mut dummy = ListNode::new(0);
        let mut tail = &mut dummy;

        for val in vals {
            tail.next = Some(Box::new(ListNode::new(val)));
            tail = tail.next.as_mut().unwrap();
        }

        dummy.next
    }
}
```
