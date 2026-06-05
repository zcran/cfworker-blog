---
title: "leetcode-递归29"
date: 2026-05-26T10:00:18+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 从链表中移除节点

给你一个链表的头节点 head 。

移除每个右侧有一个更大数值的节点。

返回修改后链表的头节点 head 。

```
impl Solution {
    /// 移除链表中所有存在右侧更大节点的节点
    ///
    /// # 问题说明
    /// 给定一个链表，移除所有满足以下条件的节点：在其右侧存在值更大的节点。
    /// 换句话说，保留的节点必须是从右向左看时的非递增序列。
    ///
    /// # 算法思路
    /// 使用递归从右向左处理：
    /// 1. 递归处理子链表，得到处理后的结果
    /// 2. 比较当前节点值与子链表头节点的值
    /// 3. 如果当前节点值更小，则丢弃当前节点；否则保留
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n) - 每个节点访问一次
    /// - 空间复杂度：O(n) - 递归调用栈深度
    ///
    /// # 示例
    /// ```
    /// // 输入: 5 -> 3 -> 2 -> 4 -> 1 -> None
    /// // 输出: 5 -> 4 -> 1 -> None
    /// // 解释: 3 右侧有 4 更大，2 右侧有 4 更大，4 右侧没有更大的，1 右侧没有更大的
    /// ```
    pub fn remove_nodes(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        // 使用递归辅助函数，更清晰的语义
        Self::remove_nodes_recursive(head)
    }

    /// 递归版本（原代码优化）
    fn remove_nodes_recursive(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        match head {
            Some(mut node) => {
                // 先处理下一个节点（从右向左构建）
                let next = Self::remove_nodes_recursive(node.next.take());

                match next {
                    Some(next_node) if node.val < next_node.val => {
                        // 当前节点值小于右侧节点，丢弃当前节点
                        Some(next_node)
                    }
                    _ => {
                        // 当前节点值 >= 右侧节点，保留当前节点
                        node.next = next;
                        Some(node)
                    }
                }
            }
            None => None,
        }
    }
}
```
