---
title: "Leetcode 归并排序1"
date: 2025-05-23T21:22:00+08:00
tags: ["leetcode", "归并排序"]
draft: false
---

## 合并 K 个升序链表
给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。


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
use std::{
    cmp::{Ord, PartialOrd, Reverse},
    collections::BinaryHeap,
};

impl Ord for ListNode {
    fn cmp(&self, other: &Self) -> std::cmp::Ordering {
        self.val.cmp(&other.val)
    }
}

impl PartialOrd for ListNode {
    fn partial_cmp(&self, other: &Self) -> Option<std::cmp::Ordering> {
        Some(self.val.cmp(&other.val))
    }
}

impl Solution {
    pub fn merge_k_lists(lists: Vec<Option<Box<ListNode>>>) -> Option<Box<ListNode>> {
        let mut node = None;
        let mut cur = &mut node;
        let mut heap = BinaryHeap::new();
        for list in lists.into_iter() {
            if let Some(x) = list {
                heap.push(Reverse(x));
            }
        }
        while let Some(mut x) = heap.pop() {
            if let Some(y) = x.0.next.take() {
                heap.push(Reverse(y));
            }
            cur = &mut cur.insert(x.0).next;
        }
        node
    }
}
```

#### 算法逻辑：
使用最小堆（通过 Reverse 包装将最大堆变为最小堆）来维护当前所有链表的最小节点
初始化时将所有链表的头节点放入堆中
每次从堆中取出最小的节点，将其加入结果链表
如果取出的节点还有后续节点，将后续节点放入堆中
重复这个过程直到堆为空

#### 关键点：
使用 Reverse 包装是因为 Rust 的 BinaryHeap 默认是最大堆，而我们需要最小堆
take() 方法用于取出 next 节点并将原位置设为 None，避免所有权问题
cur.insert(x.0).next 是一个巧妙的链表构建方式，不断向后追加节点

这个算法的时间复杂度是 O(N log K)，其中 N 是总节点数，K 是链表数量，因为每个节点都需要一次堆操作（插入或删除），而堆操作的时间复杂度是 O(log K)。