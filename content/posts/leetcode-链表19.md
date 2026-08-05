---
title: "leetcode-链表19"
date: 2026-06-29T10:55:34+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 链表随机节点

给你一个单链表，随机选择链表的一个节点，并返回相应的节点值。每个节点 被选中的概率一样 。

实现 Solution 类：

Solution(ListNode head) 使用整数数组初始化对象。

int getRandom() 从链表中随机选择一个节点并返回该节点的值。链表中所有节点被选中的概率相等。


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

use rand::{Rng, thread_rng};

/// 链表的随机节点选择器
///
/// 使用蓄水池抽样算法，无需额外存储空间，适用于任意长度的链表
/// 时间复杂度：O(n) 每次查询，空间复杂度：O(1)
pub struct Solution {
    head: Option<Box<ListNode>>,
}

impl Solution {
    /// 初始化随机选择器
    ///
    /// # 参数
    /// * `head` - 链表的头节点
    ///
    /// # 复杂度
    /// * 时间：O(1)
    /// * 空间：O(1)
    pub fn new(head: Option<Box<ListNode>>) -> Self {
        Solution { head }
    }

    /// 随机返回链表中的一个节点值
    ///
    /// 使用蓄水池抽样算法（Reservoir Sampling），保证每个节点被选中的概率相等
    ///
    /// # 复杂度
    /// * 时间：O(n) 其中 n 为链表长度
    /// * 空间：O(1)
    ///
    /// # 示例
    /// ```
    /// let obj = Solution::new(head);
    /// let ret = obj.get_random(); // 随机返回一个节点值
    /// ```
    pub fn get_random(&self) -> i32 {
        // 蓄水池抽样：遍历链表，每个节点以 1/i 的概率替换当前选择
        let mut rng = thread_rng();
        let mut result = 0;
        let mut count = 0;
        let mut current = &self.head;

        while let Some(node) = current {
            count += 1;
            // 对于第 i 个节点，以 1/i 的概率选中它
            if rng.gen_range(0..count) == 0 {
                result = node.val;
            }
            current = &node.next;
        }

        result
    }
}

/**
 * Your Solution object will be instantiated and called as such:
 * let obj = Solution::new(head);
 * let ret_1: i32 = obj.get_random();
**/
```
