---
title: "leetcode-递归38"
date: 2026-05-26T10:00:18+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 回文链表


给定一个链表的 头节点 head ，请判断其是否为回文链表。

如果一个链表是回文，那么链表节点序列从前往后看和从后往前看是相同的。

```
impl Solution {
    pub fn is_palindrome(head: Option<Box<ListNode>>) -> bool {
        // 引入标准库中的双端队列（VecDeque）
        use std::collections::VecDeque;

        // 如果链表为空，返回 false
        // 注意：题目通常规定空链表也算回文，这里返回 false 可能不符合预期
        if head.is_none() {
            return false;
        }

        // 创建可变变量 current，持有链表的所有权
        let mut current = head;
        // 创建一个双端队列，用于存储链表中所有节点的值
        let mut deque = VecDeque::new();

        // 遍历原链表，将所有节点的值存入双端队列
        while let Some(node) = current {
            // 将当前节点的值添加到队列尾部
            deque.push_back(node.val);
            // 移动到下一个节点（注意：这里使用了 node.next，会移动所有权）
            current = node.next;
        }

        // 如果队列长度只有 1，说明只有一个节点，肯定是回文
        if deque.len() == 1 {
            return true;
        }

        // 当队列长度大于 1 时，循环比较
        while deque.len() > 1 {
            // 从队列尾部取出一个值，从队列头部取出一个值
            // 如果两者不相等，说明不是回文，返回 false
            if deque.pop_back().unwrap() != deque.pop_front().unwrap() {
                return false;
            }
        }

        // 所有比较都相等，说明是回文
        return true;
    }
}
```
