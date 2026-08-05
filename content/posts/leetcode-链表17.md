---
title: "leetcode-链表17"
date: 2026-06-29T10:55:34+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 奇偶链表

给定单链表的头节点 head ，将所有索引为奇数的节点和索引为偶数的节点分别分组，保持它们原有的相对顺序，然后把偶数索引节点分组连接到奇数索引节点分组之后，返回重新排序的链表。

第一个节点的索引被认为是 奇数 ， 第二个节点的索引为 偶数 ，以此类推。

请注意，偶数组和奇数组内部的相对顺序应该与输入时保持一致。

你必须在 O(1) 的额外空间复杂度和 O(n) 的时间复杂度下解决这个问题。


```
// Definition for singly-linked list.
// #[derive(PartialEq, Eq, Clone, Debug)]
// pub struct ListNode {
//   pub val: i32,
//   pub next: Option<Box<ListNode>>
// }

impl Solution {
    pub fn odd_even_list(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        if head.is_none() || head.as_ref().unwrap().next.is_none() {
            return head;
        }

        let mut odd = head.unwrap();
        let mut even = odd.next.take().unwrap();
        let (mut p_odd, mut p_even) = (&mut odd, &mut even);

        loop {
            match p_even.next.take() {
                Some(node) => {
                    p_odd.next = Some(node);
                    p_odd = p_odd.next.as_mut().unwrap();
                }
                None => break,
            }

            match p_odd.next.take() {
                Some(node) => {
                    p_even.next = Some(node);
                    p_even = p_even.next.as_mut().unwrap();
                }
                None => break,
            }
        }

        p_odd.next = Some(even);
        Some(odd)
    }
}

```
