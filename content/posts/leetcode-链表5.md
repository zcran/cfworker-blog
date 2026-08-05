---
title: "leetcode-链表5"
date: 2026-06-29T10:55:34+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 分隔链表

给你一个链表的头节点 head 和一个特定值 x ，请你对链表进行分隔，使得所有 小于 x 的节点都出现在 大于或等于 x 的节点之前。

你应当 保留 两个分区中每个节点的初始相对位置。


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
    /// 链表分区：将所有小于 x 的节点移到大于等于 x 的节点之前
    ///
    /// # 思路
    /// 使用两个链表分别收集小于 x 和大于等于 x 的节点，
    /// 最后将两个链表连接起来，保持原有相对顺序。
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n)，遍历一次链表
    /// - 空间复杂度：O(1)，只使用常量级额外空间（节点复用）
    pub fn partition(head: Option<Box<ListNode>>, x: i32) -> Option<Box<ListNode>> {
        // 哑节点简化边界处理
        let mut small_dummy = ListNode::new(0);
        let mut large_dummy = ListNode::new(0);
        let mut small = &mut small_dummy;
        let mut large = &mut large_dummy;
        let mut curr = head;

        // 遍历原链表，分配到两个分区
        while let Some(mut node) = curr {
            curr = node.next.take(); // 取出当前节点，断开原链接

            if node.val < x {
                small.next = Some(node);
                small = small.next.as_mut().unwrap();
            } else {
                large.next = Some(node);
                large = large.next.as_mut().unwrap();
            }
        }

        // 连接两个分区：小分区尾 -> 大分区头
        small.next = large_dummy.next;

        small_dummy.next
    }
}
```
