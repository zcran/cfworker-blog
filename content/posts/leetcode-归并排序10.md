---
title: "Leetcode 归并排序10"
date: 2025-05-23T21:22:00+08:00
tags: ["leetcode", "归并排序"]
draft: false
---

## 合并 K 个升序链表

给定一个链表数组，每个链表都已经按升序排列。

请将所有链表合并到一个升序链表中，返回合并后的链表。

```
impl Solution {
    pub fn merge_k_lists(lists: Vec<Option<Box<ListNode>>>) -> Option<Box<ListNode>> {
        let n = lists.len();
        if n == 0 { return None; }
        
        Self::split_list(&lists, 0, n)
    }

    fn split_list(lists: &Vec<Option<Box<ListNode>>>, beg: usize, end: usize) -> Option<Box<ListNode>> {
        if beg + 1 == end { return lists[beg].clone(); }

        let mid = (beg + end) >> 1;
        let l1 = Self::split_list(lists, beg, mid);
        let l2 = Self::split_list(lists, mid, end);

        Self::merge_list(l1, l2)
    }

    fn merge_list(l1: Option<Box<ListNode>>, l2: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        match (l1, l2) {
            (None, None) => None,
            (Some(l1), None) => Some(l1),
            (None, Some(l2)) => Some(l2),
            (Some(mut l1), Some(mut l2)) => {
                match l1.val.cmp(&l2.val) {
                    std::cmp::Ordering::Less => {
                        l1.next = Self::merge_list(l1.next, Some(l2));
                        Some(l1)
                    }
                    _ => {
                        l2.next = Self::merge_list(Some(l1), l2.next);
                        Some(l2)
                    }
                }
            }
        }
    }
}
```