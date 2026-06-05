---
title: "leetcode-递归1"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 两数相加


给你两个 非空 的链表，表示两个非负的整数。它们每位数字都是按照 逆序 的方式存储的，并且每个节点只能存储 一位 数字。

请你将两个数相加，并以相同形式返回一个表示和的链表。

你可以假设除了数字 0 之外，这两个数都不会以 0 开头。

```

impl Solution {
    pub fn add_two_numbers(
        mut l1: Option<Box<ListNode>>,
        mut l2: Option<Box<ListNode>>,
    ) -> Option<Box<ListNode>> {
        let mut dummy = Box::new(ListNode::new(0));
        let mut tail = &mut dummy;
        let mut carry = 0;

        while l1.is_some() || l2.is_some() || carry != 0 {
            let sum = carry
                + l1.as_ref().map_or(0, |n| n.val)
                + l2.as_ref().map_or(0, |n| n.val);

            tail.next = Some(Box::new(ListNode::new(sum % 10)));
            tail = tail.next.as_mut().unwrap();

            carry = sum / 10;
            l1 = l1.and_then(|n| n.next);
            l2 = l2.and_then(|n| n.next);
        }

        dummy.next
    }
}
```
