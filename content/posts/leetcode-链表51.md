---
title: "leetcode-链表51"
date: 2026-06-29T10:55:36+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 分割链表

给你一个链表的头节点 head 和一个特定值 x ，请你对链表进行分隔，使得所有 小于 x 的节点都出现在 大于或等于 x 的节点之前。

你不需要 保留 每个分区中各节点的初始相对位置。


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
    /// 链表分区：将所有小于 x 的节点移到大于等于 x 的节点之前。
    /// 注意：不要求保持各分区内节点的原始相对顺序。
    ///
    /// # 示例
    /// ```
    /// // 输入: 1 -> 4 -> 3 -> 2 -> 5 -> 2, x = 3
    /// // 输出: 1 -> 2 -> 2 -> 4 -> 3 -> 5 (相对顺序可能不同)
    /// ```
    ///
    /// # 复杂度
    /// - 时间复杂度: O(n)
    /// - 空间复杂度: O(1)
    pub fn partition(head: Option<Box<ListNode>>, x: i32) -> Option<Box<ListNode>> {
        // 使用两个哨兵节点简化链表操作
        let mut small_dummy = Box::new(ListNode::new(0));
        let mut large_dummy = Box::new(ListNode::new(0));

        // 可变指针分别指向两个链表的尾部
        let mut small_tail = &mut small_dummy;
        let mut large_tail = &mut large_dummy;

        let mut current = head;

        // 遍历原链表，按条件分配到两个链表中
        while let Some(mut node) = current {
            // 取出当前节点的下一个节点，断开连接
            let next = node.next.take();

            if node.val < x {
                // 添加到小链表尾部
                small_tail.next = Some(node);
                small_tail = small_tail.next.as_mut().unwrap();
            } else {
                // 添加到大链表尾部
                large_tail.next = Some(node);
                large_tail = large_tail.next.as_mut().unwrap();
            }

            current = next;
        }

        // 拼接两个链表：小链表尾指向大链表头
        // 注意：大链表尾已经是 None（因为节点都是新取的，next 为 None）
        small_tail.next = large_dummy.next.take();

        // 返回小链表的头节点（跳过哨兵）
        small_dummy.next
    }
}
```
