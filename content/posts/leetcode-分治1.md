---
title: "leetcode-分治1"
date: 2026-06-13T10:51:38+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 合并 K 个升序链表

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。



```
impl Solution {
    /// 合并 K 个升序链表
    ///
    /// 使用分治法（归并思想）将问题分解为两两合并
    /// 时间复杂度：O(N log K)，其中 N 为总节点数，K 为链表数量
    /// 空间复杂度：O(log K) 递归栈空间
    pub fn merge_k_lists(lists: Vec<Option<Box<ListNode>>>) -> Option<Box<ListNode>> {
        let len = lists.len();
        if len == 0 {
            return None;
        }
        Self::divide_and_conquer(&mut lists.into_iter().collect::<Vec<_>>(), 0, len - 1)
    }

    /// 分治法：将区间 [l, r] 的链表合并为一个
    fn divide_and_conquer(lists: &mut [Option<Box<ListNode>>], l: usize, r: usize) -> Option<Box<ListNode>> {
        if l == r {
            return lists[l].take();
        }

        let mid = l + (r - l) / 2;
        let left = Self::divide_and_conquer(lists, l, mid);
        let right = Self::divide_and_conquer(lists, mid + 1, r);
        Self::merge_two(left, right)
    }

    /// 合并两个升序链表
    fn merge_two(mut l1: Option<Box<ListNode>>, mut l2: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        let mut dummy = ListNode::new(0);
        let mut tail = &mut dummy;

        while let (Some(node1), Some(node2)) = (l1.as_ref(), l2.as_ref()) {
            if node1.val < node2.val {
                // 取 l1 的当前节点
                let next = l1.as_mut().unwrap().next.take();
                tail.next = l1;
                l1 = next;
            } else {
                // 取 l2 的当前节点
                let next = l2.as_mut().unwrap().next.take();
                tail.next = l2;
                l2 = next;
            }
            tail = tail.next.as_mut().unwrap();
        }

        // 连接剩余部分
        tail.next = l1.or(l2);
        dummy.next
    }
}
```
