---
title: "leetcode-链表30"
date: 2026-06-29T10:55:35+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 交换链表中的节点

给你链表的头节点 head 和一个整数 k 。

交换 链表正数第 k 个节点和倒数第 k 个节点的值后，返回链表的头节点（链表 从 1 开始索引）。


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
    /// 交换链表中正数第 k 个节点和倒数第 k 个节点的值
    ///
    /// # 算法思路
    /// 1. 将链表所有值提取到 Vec 中，便于随机访问
    /// 2. 交换正数第 k 个和倒数第 k 个值
    /// 3. 根据交换后的值重建链表
    ///
    /// # 复杂度
    /// - 时间：O(n)，n 为链表长度
    /// - 空间：O(n)，用于存储值的 Vec
    ///
    /// # 优点
    /// - 代码简洁直观，无需处理复杂的可变引用
    /// - 安全，无 unsafe 代码
    pub fn swap_nodes(head: Option<Box<ListNode>>, k: i32) -> Option<Box<ListNode>> {
        // 提取所有节点值到 Vec
        let mut values = Vec::new();
        let mut curr = head;
        while let Some(node) = curr {
            values.push(node.val);
            curr = node.next;
        }

        let len = values.len();
        // 处理空链表或 k 超出范围（题目保证 k 有效，但防御性处理）
        if len == 0 || k as usize > len {
            return None;
        }

        // 交换正数第 k 个（索引 k-1）和倒数第 k 个（索引 len-k）的值
        let left_idx = (k - 1) as usize;
        let right_idx = len - k as usize;
        values.swap(left_idx, right_idx);

        // 从后向前重建链表（头插法，因为 Val 顺序已正确）
        let mut new_head = None;
        for val in values.into_iter().rev() {
            new_head = Some(Box::new(ListNode {
                val,
                next: new_head,
            }));
        }
        new_head
    }
}
```
