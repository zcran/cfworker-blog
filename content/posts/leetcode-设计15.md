---
title: "leetcode-设计15"
date: 2026-07-14T10:41:25+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 在受污染的二叉树中查找元素

给出一个满足下述规则的二叉树：

1. root.val == 0

2. 对于任意 treeNode：
    1. 如果 treeNode.val 为 x 且 treeNode.left != null，那么 treeNode.left.val == 2 * x + 1
    2. 如果 treeNode.val 为 x 且 treeNode.right != null，那么 treeNode.right.val == 2 * x + 2

现在这个二叉树受到「污染」，所有的 treeNode.val 都变成了 -1。

请你先还原二叉树，然后实现 FindElements 类：

FindElements(TreeNode* root) 用受污染的二叉树初始化对象，你需要先把它还原。

bool find(int target) 判断目标值 target 是否存在于还原后的二叉树中并返回结果。


```
use std::cell::RefCell;
use std::collections::HashSet;
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

/// 还原被污染的完全二叉树，支持 O(1) 查询
struct FindElements {
    /// 存储所有还原后的节点值
    values: HashSet<i32>,
}

impl FindElements {
    /// 使用受污染的二叉树根节点初始化，自动还原
    ///
    /// 还原规则：根节点值为 0，左子节点 = 2*x + 1，右子节点 = 2*x + 2
    /// 时间复杂度: O(n)，空间复杂度: O(n)
    pub fn new(root: Option<Rc<RefCell<TreeNode>>>) -> Self {
        let mut values = HashSet::new();
        Self::recover(&root, 0, &mut values);
        Self { values }
    }

    /// 递归还原二叉树并收集所有节点值
    fn recover(node: &Option<Rc<RefCell<TreeNode>>>, val: i32, values: &mut HashSet<i32>) {
        if let Some(node_ref) = node {
            let node = node_ref.borrow();
            values.insert(val);

            // 根据规则计算子节点值
            Self::recover(&node.left, val * 2 + 1, values);
            Self::recover(&node.right, val * 2 + 2, values);
        }
    }

    /// 判断目标值是否存在于还原后的二叉树中
    ///
    /// 时间复杂度: O(1)
    pub fn find(&self, target: i32) -> bool {
        self.values.contains(&target)
    }
}
```
