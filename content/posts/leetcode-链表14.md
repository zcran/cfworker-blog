---
title: "leetcode-链表14"
date: 2026-06-29T10:55:34+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 对链表进行插入排序

给定单个链表的头 head ，使用 插入排序 对链表进行排序，并返回 排序后链表的头 。

插入排序 算法的步骤:

1. 插入排序是迭代的，每次只移动一个元素，直到所有元素可以形成一个有序的输出列表。

2. 每次迭代中，插入排序只从输入数据中移除一个待排序的元素，找到它在序列中适当的位置，并将其插入。

3. 重复直到所有输入数据插入完为止。

下面是插入排序算法的一个图形示例。部分排序的列表(黑色)最初只包含列表中的第一个元素。每次迭代时，从输入数据中删除一个元素(红色)，并就地插入已排序的列表中。

对链表进行插入排序。


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
    /// 对链表进行插入排序
    ///
    /// # 思路
    /// 使用哑节点作为排序链表的头部，遍历原链表中的每个节点，
    /// 在已排序链表中找到合适的位置插入，保持有序。
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n²)，最坏情况需要遍历已排序链表找插入位置
    /// - 空间复杂度：O(1)，原地排序，只使用常数级额外空间
    pub fn insertion_sort_list(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        // 哑节点简化插入操作
        let mut sorted_head = Some(Box::new(ListNode::new(0)));
        let mut curr = head;

        while let Some(mut node) = curr {
            // 取出当前节点，断开与原链表的连接
            curr = node.next.take();

            // 在已排序链表中找到插入位置
            let mut prev = &mut sorted_head;
            while let Some(next) = &prev.as_ref().unwrap().next {
                if next.val < node.val {
                    prev = &mut prev.as_mut().unwrap().next;
                } else {
                    break;
                }
            }

            // 将节点插入到 prev 之后
            let tail = prev.as_mut().unwrap().next.take();
            node.next = tail;
            prev.as_mut().unwrap().next = Some(node);
        }

        // 返回排序后的链表（跳过哑节点）
        sorted_head.unwrap().next
    }
}
```
