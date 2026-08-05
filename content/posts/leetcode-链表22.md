---
title: "leetcode-链表22"
date: 2026-06-29T10:55:34+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 分隔链表

给你一个头结点为 head 的单链表和一个整数 k ，请你设计一个算法将链表分隔为 k 个连续的部分。

每部分的长度应该尽可能的相等：任意两部分的长度差距不能超过 1 。这可能会导致有些部分为 null 。

这 k 个部分应该按照在链表中出现的顺序排列，并且排在前面的部分的长度应该大于或等于排在后面的长度。

返回一个由上述 k 部分组成的数组。


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
    /// 将链表分割成 k 个连续部分
    ///
    /// # 分割规则
    /// - 每部分长度尽可能相等，任意两部分长度差不超过 1
    /// - 排在前面的部分长度 >= 后面的部分
    /// - 部分可能为 null（当 k > 链表长度时）
    ///
    /// # 算法
    /// 1. 计算链表总长度 n
    /// 2. 计算基础长度 base = n / k，额外长度 extra = n % k
    /// 3. 前 extra 部分长度为 base + 1，剩余为 base
    ///
    /// # 复杂度
    /// - 时间：O(n)，n 为链表长度
    /// - 空间：O(k)，存储 k 个部分的头节点
    pub fn split_list_to_parts(head: Option<Box<ListNode>>, k: i32) -> Vec<Option<Box<ListNode>>> {
        let k = k as usize;

        // 计算链表长度
        let mut len = 0;
        let mut curr = &head;
        while let Some(node) = curr {
            len += 1;
            curr = &node.next;
        }

        // 计算每部分长度
        let base_size = len / k;
        let extra_count = len % k;

        let mut result = Vec::with_capacity(k);
        let mut curr = head;

        // 分割链表
        for i in 0..k {
            // 前 extra_count 部分多一个节点
            let part_size = base_size + if i < extra_count { 1 } else { 0 };

            // 取出当前部分的头节点
            let mut part_head = curr.take();
            let mut part_tail = &mut part_head;

            // 移动到当前部分的最后一个节点
            for _ in 0..part_size.saturating_sub(1) {
                if let Some(node) = part_tail {
                    part_tail = &mut node.next;
                }
            }

            // 断开当前部分与剩余链表的连接
            if let Some(node) = part_tail {
                curr = node.next.take();
            }

            result.push(part_head);
        }

        result
    }
}
```
