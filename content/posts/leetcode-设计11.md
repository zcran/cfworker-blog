---
title: "leetcode-设计11"
date: 2026-07-14T10:41:25+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 完全二叉树插入器

完全二叉树 是每一层（除最后一层外）都是完全填充（即，节点数达到最大）的，并且所有的节点都尽可能地集中在左侧。

设计一种算法，将一个新节点插入到一棵完全二叉树中，并在插入后保持其完整。

实现 CBTInserter 类:

CBTInserter(TreeNode root) 使用头节点为 root 的给定树初始化该数据结构；
CBTInserter.insert(int v)  向树中插入一个值为 Node.val == val的新节点 TreeNode。使树保持完全二叉树的状态，并返回插入节点 TreeNode 的父节点的值；
CBTInserter.get_root() 将返回树的头节点。


```
use std::cell::RefCell;
use std::rc::Rc;

// Definition for a binary tree node.
// #[derive(Debug, PartialEq, Eq)]
// pub struct TreeNode {
//   pub val: i32,
//   pub left: Option<Rc<RefCell<TreeNode>>>,
//   pub right: Option<Rc<RefCell<TreeNode>>>,
// }
// impl TreeNode {
//   #[inline]
//   pub fn new(val: i32) -> Self {
//     TreeNode {
//       val,
//       left: None,
//       right: None,
//     }
//   }
// }

/// 完全二叉树插入器，支持 O(1) 插入和 O(n) 初始化
struct CBTInserter {
    /// 按层序遍历顺序存储所有节点（数组模拟完全二叉树）
    nodes: Vec<Option<Rc<RefCell<TreeNode>>>>,
}

impl CBTInserter {
    /// 使用完全二叉树的根节点初始化
    ///
    /// 通过层序遍历构建节点数组
    /// 时间复杂度: O(n)，空间复杂度: O(n)
    pub fn new(root: Option<Rc<RefCell<TreeNode>>>) -> Self {
        let mut nodes = Vec::new();
        nodes.push(root);

        let mut i = 0;
        while i < nodes.len() {
            // 先克隆节点，避免借用冲突
            let node_clone = nodes[i].clone();
            if let Some(node) = node_clone {
                let node_ref = node.borrow();
                if let Some(left) = node_ref.left.clone() {
                    nodes.push(Some(left));
                }
                if let Some(right) = node_ref.right.clone() {
                    nodes.push(Some(right));
                }
            }
            i += 1;
        }

        Self { nodes }
    }

    /// 插入新节点，保持完全二叉树性质
    ///
    /// 根据数组索引定位父节点：
    /// - 新节点索引为 n，父节点索引为 (n-1)/2
    /// - 如果 n 为偶数，新节点是右子节点；否则是左子节点
    ///
    /// 时间复杂度: O(1)，空间复杂度: O(1)
    pub fn insert(&mut self, val: i32) -> i32 {
        let new_node = Some(Rc::new(RefCell::new(TreeNode::new(val))));
        let new_index = self.nodes.len();
        self.nodes.push(new_node.clone());

        // 找到父节点索引 (完全二叉树的数组表示)
        let parent_index = (new_index - 1) / 2;
        let parent = self.nodes[parent_index]
            .as_ref()
            .expect("Parent should exist");

        // 根据新节点索引判断左右
        if new_index % 2 == 0 {
            // 偶数索引：右子节点（因为根索引为 0，左子节点索引为奇数）
            parent.borrow_mut().right = new_node;
        } else {
            // 奇数索引：左子节点
            parent.borrow_mut().left = new_node;
        }

        parent.borrow().val
    }

    /// 返回树的根节点
    pub fn get_root(&self) -> Option<Rc<RefCell<TreeNode>>> {
        self.nodes[0].clone()
    }
}
```
