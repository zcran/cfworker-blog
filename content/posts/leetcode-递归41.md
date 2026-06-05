---
title: "leetcode-递归41"
date: 2026-05-26T10:00:18+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 训练计划 III

给定一个头节点为 head 的单链表用于记录一系列核心肌群训练编号，请将该系列训练编号 倒序 记录于链表并返回。

```
impl Solution {
    pub fn trainning_plan(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        // 递归反转链表
        Self::reverse_recursive(head, None)
    }

    // 辅助递归函数
    fn reverse_recursive(
        curr: Option<Box<ListNode>>,
        prev: Option<Box<ListNode>>
    ) -> Option<Box<ListNode>> {
        match curr {
            None => prev,  // 当前节点为空，返回反转后的头节点
            Some(mut node) => {
                // 保存下一个节点
                let next = node.next.take();
                // 当前节点指向前一个节点
                node.next = prev;
                // 递归处理下一个节点，当前节点成为新的 prev
                Self::reverse_recursive(next, Some(node))
            }
        }
    }
}
```
