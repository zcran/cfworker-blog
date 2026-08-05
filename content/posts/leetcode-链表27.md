---
title: "leetcode-链表27"
date: 2026-06-29T10:55:35+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 二进制链表转整数

给你一个单链表的引用结点 head。链表中每个结点的值不是 0 就是 1。已知此链表是一个整数数字的二进制表示形式。

请你返回该链表所表示数字的 十进制值 。

最高位 在链表的头部。


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
    /// 将二进制链表转换为十进制整数
    ///
    /// 利用二进制转十进制的左移算法：
    /// 从头节点开始遍历，每读到一个 bit，结果左移一位并加上当前 bit
    ///
    /// 示例：链表 1 -> 0 -> 1
    /// 初始化 result = 0
    /// 读 1: result = 0 * 2 + 1 = 1
    /// 读 0: result = 1 * 2 + 0 = 2
    /// 读 1: result = 2 * 2 + 1 = 5
    ///
    /// # 复杂度
    /// - 时间：O(n)，n 为链表长度
    /// - 空间：O(1)，仅使用一个整数变量
    pub fn get_decimal_value(mut head: Option<Box<ListNode>>) -> i32 {
        let mut result = 0;

        // 遍历链表，按位构建十进制值
        while let Some(node) = head {
            result = (result << 1) | node.val; // result * 2 + node.val
            head = node.next;
        }

        result
    }
}
```
