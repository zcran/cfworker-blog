---
title: "leetcode-递归3"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 合并两个有序链表


将两个升序链表合并为一个新的 升序 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。

```
impl Solution {
    /// 合并两个有序链表（迭代版 - 修正）
    ///
    /// 时间复杂度: O(m+n)
    /// 空间复杂度: O(1)
    pub fn merge_two_lists(
        mut list1: Option<Box<ListNode>>,
        mut list2: Option<Box<ListNode>>,
    ) -> Option<Box<ListNode>> {
        let mut dummy = ListNode::new(0);
        let mut tail = &mut dummy;

        // 关键修正：使用 as_mut() 借用而非移动所有权
        while let (Some(node1), Some(node2)) = (list1.as_mut(), list2.as_mut()) {
            if node1.val < node2.val {
                tail.next = list1.take();
                tail = tail.next.as_mut().unwrap();
                list1 = tail.next.take();
            } else {
                tail.next = list2.take();
                tail = tail.next.as_mut().unwrap();
                list2 = tail.next.take();
            }
        }

        tail.next = list1.or(list2);
        dummy.next
    }
}
```
