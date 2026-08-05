---
title: "leetcode-链表23"
date: 2026-06-29T10:55:35+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 链表组件

给定链表头结点 head，该链表上的每个结点都有一个 唯一的整型值 。同时给定列表 nums，该列表是上述链表中整型值的一个子集。

返回列表 nums 中组件的个数，这里对组件的定义为：链表中一段最长连续结点的值（该值必须在列表 nums 中）构成的集合。


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
    /// 计算链表中的组件数量
    ///
    /// 组件定义：链表中一段最长连续节点，其值都在 nums 中
    ///
    /// # 算法思路
    /// 遍历链表，每当遇到一个节点：
    /// 1. 当前节点值在 nums 中
    /// 2. 且下一个节点为空或不在 nums 中
    /// 则说明找到一个组件的末尾，计数器加 1
    ///
    /// # 复杂度
    /// - 时间：O(n)，n 为链表长度
    /// - 空间：O(m)，m 为 nums 长度，用于存储 HashSet
    pub fn num_components(head: Option<Box<ListNode>>, nums: Vec<i32>) -> i32 {
        // 使用 HashSet 实现 O(1) 的查找
        let num_set: HashSet<_> = nums.into_iter().collect();

        let mut count = 0;
        let mut current = head.as_ref();

        while let Some(node) = current {
            // 如果当前节点在集合中，且是组件的最后一个节点
            if num_set.contains(&node.val) {
                // 检查下一个节点是否不在集合中（或者为空）
                let is_end = node.next
                    .as_ref()
                    .map_or(true, |next| !num_set.contains(&next.val));

                if is_end {
                    count += 1;
                }
            }

            current = node.next.as_ref();
        }

        count
    }
}
```
