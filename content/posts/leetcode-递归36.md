---
title: "leetcode-递归36"
date: 2026-05-26T10:00:18+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 反转链表

给定单链表的头节点 head ，请反转链表，并返回反转后的链表的头节点。


```
impl Solution {
    /// 反转单链表（迭代法）
    ///
    /// # 算法步骤
    /// 1. 初始化 prev 为 None
    /// 2. 遍历链表，每次将当前节点的 next 指向 prev
    /// 3. 更新 prev 和 head 指针
    /// 4. 返回 prev 作为新的头节点
    ///
    /// # 示例
    /// ```
    /// let list = Solution::create_list(&[1, 2, 3]);
    /// let reversed = Solution::reverse_list(list);
    /// assert_eq!(Solution::list_to_vec(reversed), vec![3, 2, 1]);
    /// ```
    pub fn reverse_list(mut head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
        let mut prev = None;  // 前一个节点

        while let Some(mut current) = head {
            // 暂存下一个节点，同时断开当前节点的 next
            let next = current.next.take();

            // 反转：将当前节点指向前一个节点
            current.next = prev;

            // 移动指针
            prev = Some(current);
            head = next;
        }

        prev  // 返回新头节点
    }
}
```
