---
title: "leetcode-链表24"
date: 2026-06-29T10:55:35+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 链表的中间结点


给你单链表的头结点 head ，请你找出并返回链表的中间结点。

如果有两个中间结点，则返回第二个中间结点。

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
    /// 寻找链表的中间节点
    ///
    /// 使用快慢指针算法：
    /// - 慢指针每次走一步，快指针每次走两步
    /// - 当快指针到达末尾时，慢指针正好在中间
    /// - 如果有两个中间节点，返回第二个（快指针从 head 开始）
    ///
    /// # 复杂度
    /// - 时间：O(n)，n 为链表长度
    /// - 空间：O(1)，只使用了两个指针
    pub fn middle_node(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        // 快慢指针都从头节点开始
        let mut slow = &head;
        let mut fast = &head;

        // 快指针每次走两步，慢指针走一步
        while let (Some(fast_node), Some(_)) = (
            fast.as_ref().and_then(|node| node.next.as_ref()),
            fast.as_ref()
        ) {
            slow = &slow.as_ref().unwrap().next;
            fast = &fast_node.next;
        }

        // 返回慢指针指向的节点（需要克隆所有权）
        slow.clone()
    }
}
```
