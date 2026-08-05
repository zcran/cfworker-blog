---
title: "leetcode-链表20"
date: 2026-06-29T10:55:34+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 两数相加 II

给你两个 非空 链表来代表两个非负整数。数字最高位位于链表开始位置。它们的每个节点只存储一位数字。将这两数相加会返回一个新的链表。

你可以假设除了数字 0 之外，这两个数字都不会以零开头。


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

use std::collections::VecDeque;

/// 两个链表表示的数字相加
///
/// 由于数字最高位在链表开始位置，需要从低位（链表尾部）开始相加
/// 使用栈（Vec）反转链表顺序，从个位开始逐位计算
///
/// # 复杂度
/// * 时间：O(m + n)，其中 m 和 n 分别是两个链表的长度
/// * 空间：O(m + n)，用于存储两个栈
impl Solution {
    pub fn add_two_numbers(
        l1: Option<Box<ListNode>>,
        l2: Option<Box<ListNode>>,
    ) -> Option<Box<ListNode>> {
        // 将链表压入栈，实现反转（个位在栈顶）
        let mut stack1 = Vec::new();
        let mut stack2 = Vec::new();

        let mut curr = l1;
        while let Some(node) = curr {
            stack1.push(node.val);
            curr = node.next;
        }

        let mut curr = l2;
        while let Some(node) = curr {
            stack2.push(node.val);
            curr = node.next;
        }

        // 从个位开始相加，结果链表通过头插法构建（高位在表头）
        let mut result = None;
        let mut carry = 0;

        // 处理两个栈和进位
        while !stack1.is_empty() || !stack2.is_empty() || carry > 0 {
            let sum = carry
                + stack1.pop().unwrap_or(0)
                + stack2.pop().unwrap_or(0);

            let digit = sum % 10;
            carry = sum / 10;

            // 头插法：新节点成为新的头节点
            result = Some(Box::new(ListNode {
                val: digit,
                next: result,
            }));
        }

        // 如果结果为 None（两个数都是 0），返回 0
        result.or_else(|| Some(Box::new(ListNode::new(0))))
    }
}
```
