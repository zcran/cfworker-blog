---
title: "leetcode-链表48"
date: 2026-06-29T10:55:36+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 移除重复节点

编写代码，移除未排序链表中的重复节点。保留最开始出现的节点。

示例1：

 输入：[1, 2, 3, 3, 2, 1]
 输出：[1, 2, 3]

示例2：

 输入：[1, 1, 1, 1, 2]
 输出：[1, 2]

提示：

链表长度在[0, 20000]范围内。
链表元素在[0, 20000]范围内。

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

use std::collections::HashSet;

impl Solution {
    /// 移除未排序链表中的重复节点，保留第一次出现的节点。
    ///
    /// # 示例
    /// ```
    /// // 输入: 1 -> 2 -> 3 -> 2 -> 1
    /// // 输出: 1 -> 2 -> 3
    /// ```
    pub fn remove_duplicate_nodes(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        // 空链表或单节点链表直接返回
        if head.is_none() || head.as_ref().unwrap().next.is_none() {
            return head;
        }

        let mut set = HashSet::new();
        let mut dummy = Box::new(ListNode::new(0));
        dummy.next = head;

        // 使用可变引用遍历，原地删除重复节点
        let mut current = &mut dummy;

        while let Some(ref mut node) = current.next {
            if set.contains(&node.val) {
                // 跳过重复节点
                current.next = node.next.take();
            } else {
                // 首次出现，记录并前进
                set.insert(node.val);
                current = current.next.as_mut().unwrap();
            }
        }

        dummy.next
    }
}
```
