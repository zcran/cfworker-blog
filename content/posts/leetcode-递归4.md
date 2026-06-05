---
title: "leetcode-递归4"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 两两交换链表中的节点


给你一个链表，两两交换其中相邻的节点，并返回交换后链表的头节点。你必须在不修改节点内部的值的情况下完成本题（即，只能进行节点交换）。


```

impl Solution {
    pub fn swap_pairs(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        head.and_then(|mut n| {
            match n.next {
                Some(mut m) => {
                    n.next = Self::swap_pairs(m.next);
                    m.next = Some(n);
                    Some(m)
                },
                None => Some(n)
            }
        })
    }
}

```
