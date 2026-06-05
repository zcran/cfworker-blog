---
title: "leetcode-递归42"
date: 2026-05-26T10:00:18+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 训练计划 IV

给定两个以 有序链表 形式记录的训练计划 l1、l2，分别记录了两套核心肌群训练项目编号，请合并这两个训练计划，按训练项目编号 升序 记录于链表并返回。

注意：新链表是通过拼接给定的两个链表的所有节点组成的。

```
impl Solution {
    // 合并两个有序链表 - 题目要求的函数名
    pub fn trainning_plan(l1: Option<Box<ListNode>>, l2: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        match (l1, l2) {
            // 情况1: l1有节点，l2为空，直接返回l1
            (Some(node1), None) => Some(node1),
            // 情况2: l1为空，l2有节点，直接返回l2
            (None, Some(node2)) => Some(node2),
            // 情况3: 两个链表都有节点
            (Some(mut node1), Some(mut node2)) => {
                // 比较两个节点的值，选择较小的作为头节点
                if node1.val < node2.val {
                    // node1的值更小，将node1作为当前节点
                    // 取出node1的下一个节点，继续递归合并
                    let next = node1.next.take();
                    node1.next = Solution::trainning_plan(next, Some(node2));
                    Some(node1)
                } else {
                    // node2的值更小或相等，将node2作为当前节点
                    // 取出node2的下一个节点，继续递归合并
                    let next = node2.next.take();
                    node2.next = Solution::trainning_plan(Some(node1), next);
                    Some(node2)
                }
            },
            // 情况4: 两个链表都为空，返回None
            _ => None,
        }
    }
}
```
