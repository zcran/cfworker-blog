---
title: "leetcode-链表3"
date: 2026-06-29T10:55:34+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 删除排序链表中的重复元素

给定一个已排序的链表的头 head ， 删除所有重复的元素，使每个元素只出现一次 。返回 已排序的链表 。


```
impl Solution {
    pub fn delete_duplicates(mut head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        // 使用可变引用遍历链表，原地删除重复节点
        let mut current = &mut head;
        while let Some(node) = current {
            // 跳过所有与当前节点值相同的后续节点
            while let Some(next) = node.next.as_mut() {
                if next.val == node.val {
                    // 删除重复节点（将当前节点的 next 指向下下个节点）
                    node.next = next.next.take();
                } else {
                    break; // 值不同，结束内层循环
                }
            }
            // 移动到下一个不同值的节点
            current = &mut node.next;
        }
        head
    }
}
```
