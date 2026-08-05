---
title: "leetcode-链表33"
date: 2026-06-29T10:55:35+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 删除链表的中间节点


给你一个链表的头节点 head 。删除 链表的 中间节点 ，并返回修改后的链表的头节点 head 。

长度为 n 链表的中间节点是从头数起第 ⌊n / 2⌋ 个节点（下标从 0 开始），其中 ⌊x⌋ 表示小于或等于 x 的最大整数。

对于 n = 1、2、3、4 和 5 的情况，中间节点的下标分别是 0、1、1、2 和 2 。


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
    /// 删除链表的中间节点，返回新链表头
    ///
    /// # 算法
    /// 1. 先遍历一次计算链表长度 n
    /// 2. 若 n <= 1，删除后为空，直接返回 None
    /// 3. 中间节点下标为 floor(n/2)，其前驱下标为 floor(n/2) - 1
    /// 4. 从虚拟头节点开始移动 n/2 步，到达前驱节点
    /// 5. 删除该前驱节点的下一个节点
    ///
    /// # 复杂度
    /// - 时间：O(n)
    /// - 空间：O(1)
    pub fn delete_middle(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        // 计算链表长度
        let mut len = 0;
        let mut cur = &head;
        while let Some(node) = cur {
            len += 1;
            cur = &node.next;
        }

        // 空链表或只有一个节点 → 删除后为空
        if len <= 1 {
            return None;
        }

        // 使用虚拟头节点简化操作
        let mut dummy = Box::new(ListNode { val: 0, next: head });
        let mut prev = &mut dummy;

        // 移动到中间节点的前驱节点
        // 前驱下标 = len / 2 - 1，从 dummy 到该前驱需要移动 len / 2 次
        for _ in 0..len / 2 {
            prev = prev.next.as_mut().unwrap();
        }

        // 删除中间节点：跳过 prev.next
        let to_delete = prev.next.as_mut().unwrap();
        prev.next = to_delete.next.take();

        dummy.next
    }
}
```
