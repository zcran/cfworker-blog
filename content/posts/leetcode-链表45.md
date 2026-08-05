---
title: "leetcode-链表45"
date: 2026-06-29T10:55:36+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 训练计划 II


给定一个头节点为 head 的链表用于记录一系列核心肌群训练项目编号，请查找并返回倒数第 cnt 个训练项目编号对应的节点。

```
impl Solution {
    pub fn training_plan(head: Option<Box<ListNode>>, cnt: i32) -> Option<Box<ListNode>> {
        // 使用双指针：fast 先走 cnt 步，然后 slow 和 fast 同步移动
        let mut fast = head.as_ref();
        let mut slow = head.as_ref();

        // fast 先走 cnt 步
        for _ in 0..cnt {
            fast = fast?.next.as_ref();
        }

        // 当 fast 到达末尾时，slow 指向倒数第 cnt 个节点
        while let Some(f) = fast {
            fast = f.next.as_ref();
            slow = slow.unwrap().next.as_ref();
        }

        // 返回找到的节点（需要克隆）
        slow.cloned()
    }
}
```
