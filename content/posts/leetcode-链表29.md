---
title: "leetcode-链表29"
date: 2026-06-29T10:55:35+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 合并两个链表

给你两个链表 list1 和 list2 ，它们包含的元素分别为 n 个和 m 个。

请你将 list1 中下标从 a 到 b 的全部节点都删除，并将list2 接在被删除节点的位置。

下图中蓝色边和节点展示了操作后的结果：
![](../pic/fig1.png)

请你返回结果链表的头指针。

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
    /// 将 list2 插入到 list1 的 a 到 b 位置之间
    ///
    /// # 算法步骤
    /// 1. 找到第 a-1 个节点 prev_a（即要删除部分的前驱）
    /// 2. 将 prev_a.next 指向 list2，保存原来要删除部分的头节点
    /// 3. 遍历到 list2 的末尾
    /// 4. 找到第 b 个节点 node_b（即要删除部分的后继）
    /// 5. 将 list2 末尾指向 node_b
    ///
    /// # 复杂度
    /// - 时间：O(n + m)，n 为 list1 长度，m 为 list2 长度
    /// - 空间：O(1)，仅使用指针操作
    pub fn merge_in_between(
        mut list1: Option<Box<ListNode>>,
        a: i32,
        b: i32,
        list2: Option<Box<ListNode>>,
    ) -> Option<Box<ListNode>> {
        // 1. 找到第 a-1 个节点（要删除部分的前驱）
        let mut prev_a = &mut list1;
        for _ in 0..a - 1 {
            prev_a = &mut prev_a.as_mut().unwrap().next;
        }

        // 2. 保存要删除部分的头节点，并将 prev_a.next 指向 list2
        let mut removed_head = prev_a.as_mut().unwrap().next.take();
        prev_a.as_mut().unwrap().next = list2;

        // 3. 遍历到 list2 的末尾
        let mut tail = prev_a;
        while tail.as_mut().unwrap().next.is_some() {
            tail = &mut tail.as_mut().unwrap().next;
        }

        // 4. 找到第 b 个节点（要删除部分的后继）
        let mut node_b = removed_head;
        // 跳过 a 到 b 的节点，node_b 最终指向 b+1 位置
        for _ in a..=b {
            node_b = node_b.as_mut().unwrap().next.take();
        }

        // 5. 将 list2 末尾连接到 node_b
        tail.as_mut().unwrap().next = node_b;

        list1
    }
}
```
