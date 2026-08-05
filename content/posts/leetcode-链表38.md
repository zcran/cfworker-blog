---
title: "leetcode-链表38"
date: 2026-06-29T10:55:35+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 从链表中移除在数组中存在的节点

给你一个整数数组 nums 和一个链表的头节点 head。从链表中移除所有存在于 nums 中的节点后，返回修改后的链表的头节点。


```
use std::collections::HashSet;

impl Solution {
    pub fn modified_list(nums: Vec<i32>, mut head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        // 构建哈希集合用于 O(1) 查找
        let to_remove: HashSet<i32> = nums.into_iter().collect();

        // 使用哨兵节点简化删除操作
        let mut dummy = Box::new(ListNode::new(0));
        let mut tail = &mut dummy;

        // 遍历原链表，将需要保留的节点连接到新链表
        while let Some(mut node) = head {
            let next = node.next.take(); // 分离当前节点和后续节点
            if !to_remove.contains(&node.val) {
                tail.next = Some(node);
                tail = tail.next.as_mut().unwrap();
            }
            // 如果节点被删除，它的 next 已经被 take，无需额外处理
            head = next;
        }

        dummy.next
    }
}
```
