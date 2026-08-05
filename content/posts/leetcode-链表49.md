---
title: "leetcode-链表49"
date: 2026-06-29T10:55:36+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 返回倒数第 k 个节点

实现一种算法，找出单向链表中倒数第 k 个节点。返回该节点的值。


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
    /// 找出单向链表中倒数第 k 个节点的值（k 从 1 开始计数）。
    ///
    /// # 示例
    /// ```
    /// // 链表: 1 -> 2 -> 3 -> 4 -> 5, k = 2
    /// // 返回: 4
    /// ```
    ///
    /// # 复杂度
    /// - 时间复杂度: O(n)，其中 n 为链表长度
    /// - 空间复杂度: O(1)
    pub fn kth_to_last(head: Option<Box<ListNode>>, k: i32) -> i32 {
        // 使用双指针（快慢指针）技术
        // 快指针先走 k 步，然后快慢指针同步移动
        // 当快指针到达末尾时，慢指针指向倒数第 k 个节点

        let mut fast = head.as_ref();
        let mut slow = head.as_ref();

        // 快指针先走 k 步
        for _ in 0..k {
            match fast {
                Some(node) => fast = node.next.as_ref(),
                None => panic!("k 大于链表长度"),
            }
        }

        // 同步移动快慢指针
        while let Some(node) = fast {
            fast = node.next.as_ref();
            slow = slow.unwrap().next.as_ref();
        }

        // 慢指针指向倒数第 k 个节点
        slow.unwrap().val
    }
}
```
