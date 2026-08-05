---
title: "leetcode-链表35"
date: 2026-06-29T10:55:35+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 合并零之间的节点

给你一个链表的头节点 head ，该链表包含由 0 分隔开的一连串整数。链表的 开端 和 末尾 的节点都满足 Node.val == 0 。

对于每两个相邻的 0 ，请你将它们之间的所有节点合并成一个节点，其值是所有已合并节点的值之和。然后将所有 0 移除，修改后的链表不应该含有任何 0 。

 返回修改后链表的头节点 head 。


```
impl Solution {
    pub fn merge_nodes(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        // 使用哑节点简化结果链表的构建
        let mut result_dummy = Box::new(ListNode::new(0));
        let mut result_tail = &mut result_dummy;

        let mut current = head.unwrap().next; // 跳过第一个 0
        let mut segment_sum = 0;

        while let Some(mut node) = current {
            if node.val == 0 {
                // 遇到 0 表示一个段结束，将累积和作为新节点加入结果
                if segment_sum != 0 { // 避免添加值为 0 的节点（理论上不会发生，但防御性编程）
                    result_tail.next = Some(Box::new(ListNode::new(segment_sum)));
                    result_tail = result_tail.next.as_mut().unwrap();
                    segment_sum = 0;
                }
            } else {
                segment_sum += node.val;
            }
            current = node.next.take(); // 推进到下一个节点，同时释放当前节点的所有权
        }

        result_dummy.next
    }
}
```
