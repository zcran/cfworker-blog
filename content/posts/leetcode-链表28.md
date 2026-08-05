---
title: "leetcode-链表28"
date: 2026-06-29T10:55:35+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 二叉树中的链表

给你一棵以 root 为根的二叉树和一个 head 为第一个节点的链表。

如果在二叉树中，存在一条一直向下的路径，且每个点的数值恰好一一对应以 head 为首的链表中每个节点的值，那么请你返回 True ，否则返回 False 。

一直向下的路径的意思是：从树中某个节点开始，一直连续向下的路径。


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

use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 判断链表中是否存在一条从二叉树某个节点开始的向下路径与之匹配
    ///
    /// # 算法思路
    /// 1. 从二叉树的每个节点开始，尝试匹配链表（dfs）
    /// 2. 如果当前节点匹配失败，递归尝试左子树和右子树
    ///
    /// # 复杂度
    /// - 时间：O(N * M)，N 为树节点数，M 为链表长度（最坏情况）
    /// - 空间：O(max(H, M))，递归栈深度
    pub fn is_sub_path(
        head: Option<Box<ListNode>>,
        root: Option<Rc<RefCell<TreeNode>>>,
    ) -> bool {
        // 空链表视为匹配成功
        if head.is_none() {
            return true;
        }
        // 空树且链表非空则匹配失败
        if root.is_none() {
            return false;
        }

        let root_ref = root.as_ref().unwrap();
        let node = root_ref.borrow();

        // 从当前节点开始匹配 或 在左子树中匹配 或 在右子树中匹配
        Self::match_path(&head, root.as_ref())
            || Self::is_sub_path(head.clone(), node.left.clone())
            || Self::is_sub_path(head.clone(), node.right.clone())
    }

    /// 从当前树节点开始，尝试匹配链表路径
    fn match_path(
        head: &Option<Box<ListNode>>,
        root: Option<&Rc<RefCell<TreeNode>>>,
    ) -> bool {
        // 匹配成功：链表为空
        if head.is_none() {
            return true;
        }
        // 匹配失败：树节点为空
        if root.is_none() {
            return false;
        }

        let list_node = head.as_ref().unwrap();
        let tree_node = root.unwrap().borrow();

        // 当前值匹配，且（左子树匹配 或 右子树匹配）
        list_node.val == tree_node.val
            && (Self::match_path(&list_node.next, tree_node.left.as_ref())
                || Self::match_path(&list_node.next, tree_node.right.as_ref()))
    }
}
```
