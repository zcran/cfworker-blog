---
title: "leetcode-链表34"
date: 2026-06-29T10:55:35+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 链表最大孪生和

在一个大小为 n 且 n 为 偶数 的链表中，对于 0 <= i <= (n / 2) - 1 的 i ，第 i 个节点（下标从 0 开始）的孪生节点为第 (n-1-i) 个节点 。

比方说，n = 4 那么节点 0 是节点 3 的孪生节点，节点 1 是节点 2 的孪生节点。这是长度为 n = 4 的链表中所有的孪生节点。

孪生和 定义为一个节点和它孪生节点两者值之和。

给你一个长度为偶数的链表的头节点 head ，请你返回链表的 最大孪生和 。


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
    pub fn pair_sum(head: Option<Box<ListNode>>) -> i32 {
        // 1. 计算链表长度
        let mut len = 0;
        let mut cur = head.as_ref();
        while let Some(node) = cur {
            len += 1;
            cur = node.next.as_ref();
        }

        // 2. 找到中点，将链表分成两半
        let mut head = head; // 重新绑定为可变
        let mut cur = &mut head;
        for _ in 0..len / 2 {
            // 安全地移动到下一个节点的 next 字段
            cur = &mut cur.as_mut().unwrap().next;
        }
        // cur 现在指向中间节点的前一个节点的 next 字段，
        // 即指向后半部分链表的头节点的 Option。
        let second_half = cur.take(); // 取出后半部分，同时将前半部分末尾断开

        // 3. 反转后半部分
        let mut prev = None;
        let mut curr = second_half;
        while let Some(mut node) = curr {
            let next = node.next.take();
            node.next = prev;
            prev = Some(node);
            curr = next;
        }
        let second_rev = prev;

        // 4. 同时遍历前半部分和反转后的后半部分，计算最大孪生和
        let mut max_sum = 0;
        let mut first = &head;
        let mut second = &second_rev;
        while let (Some(f), Some(s)) = (first.as_ref(), second.as_ref()) {
            let sum = f.val + s.val;
            if sum > max_sum {
                max_sum = sum;
            }
            first = &f.next;
            second = &s.next;
        }

        max_sum
    }
}
```
