---
title: "leetcode-链表4"
date: 2026-06-29T10:55:34+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 删除排序链表中的重复元素 II

给定一个已排序的链表的头 head ， 删除原始链表中所有重复数字的节点，只留下不同的数字 。返回 已排序的链表 。


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
    /// 删除排序链表中所有重复数字的节点，只保留出现一次的数字
    ///
    /// # 思路
    /// 使用双指针法，通过哑节点(dummy)简化边界处理。
    /// `prev`指向已处理部分的末尾，`curr`遍历剩余节点。
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n)，只需一次遍历
    /// - 空间复杂度：O(1)，只使用常数级额外空间
    pub fn delete_duplicates(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        let mut dummy = ListNode::new(0);
        dummy.next = head;
        let mut prev = &mut dummy;

        while let Some(curr) = prev.next.as_mut() {
            // 检查当前节点是否与下一个节点重复
            let has_duplicate = curr.next.as_ref()
                .map_or(false, |next| next.val == curr.val);

            if has_duplicate {
                // 跳过所有值相同的节点
                let duplicate_val = curr.val;
                while let Some(node) = prev.next.as_mut() {
                    if node.val != duplicate_val {
                        break;
                    }
                    // 移除当前节点
                    prev.next = node.next.take();
                }
            } else {
                // 没有重复，移动指针
                prev = prev.next.as_mut().unwrap();
            }
        }

        dummy.next
    }
}
```
