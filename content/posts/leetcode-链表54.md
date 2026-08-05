---
title: "leetcode-链表54"
date: 2026-06-29T10:55:36+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 特定深度节点链表

给定一棵二叉树，设计一个算法，创建含有某一深度上所有节点的链表（比如，若一棵树的深度为 D，则会创建出 D 个链表）。返回一个包含所有深度的链表的数组。


```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::VecDeque;

// Definition for a binary tree node.
// #[derive(Debug, PartialEq, Eq)]
// pub struct TreeNode {
//   pub val: i32,
//   pub left: Option<Rc<RefCell<TreeNode>>>,
//   pub right: Option<Rc<RefCell<TreeNode>>>,
// }
//
// impl TreeNode {
//   #[inline]
//   pub fn new(val: i32) -> Self {
//     TreeNode {
//       val,
//       left: None,
//       right: None
//     }
//   }
// }

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
    /// 将二叉树的每一层转换为一个链表
    ///
    /// # 算法
    /// 使用广度优先搜索（BFS）层序遍历二叉树，每层节点构成一个链表
    ///
    /// # 示例
    /// ```
    /// // 二叉树:     1
    /// //           / \
    /// //          2   3
    /// //         / \   \
    /// //        4   5   6
    /// // 返回: [1] -> [2,3] -> [4,5,6]
    /// ```
    ///
    /// # 复杂度
    /// - 时间复杂度: O(n)，n 为节点数
    /// - 空间复杂度: O(n)
    pub fn list_of_depth(tree: Option<Rc<RefCell<TreeNode>>>) -> Vec<Option<Box<ListNode>>> {
        // 处理空树
        if tree.is_none() {
            return Vec::new();
        }

        let mut result = Vec::new();
        let mut queue = VecDeque::new();
        queue.push_back(tree);

        // 层序遍历
        while !queue.is_empty() {
            let level_size = queue.len();

            // 使用哨兵节点简化链表构建
            let mut dummy = Box::new(ListNode::new(0));
            let mut tail = &mut dummy;

            // 处理当前层的所有节点
            for _ in 0..level_size {
                if let Some(node) = queue.pop_front().unwrap() {
                    let node_ref = node.borrow();

                    // 将子节点加入队列供下一层使用
                    if let Some(left) = node_ref.left.clone() {
                        queue.push_back(Some(left));
                    }
                    if let Some(right) = node_ref.right.clone() {
                        queue.push_back(Some(right));
                    }

                    // 将当前节点值添加到链表末尾
                    tail.next = Some(Box::new(ListNode::new(node_ref.val)));
                    tail = tail.next.as_mut().unwrap();
                }
            }

            // 取出哨兵节点的下一个节点（即真正的链表头）
            result.push(dummy.next.take());
        }

        result
    }
}
```
