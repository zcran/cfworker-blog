---
title: "leetcode-二叉搜索树38"
date: 2026-05-14T20:16:16+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 合法二叉搜索树

实现一个函数，检查一棵二叉树是否为二叉搜索树。

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 验证二叉树是否为有效的二叉搜索树（BST）
    /// 算法：中序遍历（迭代），检查是否严格递增
    /// 时间复杂度 O(n)，空间复杂度 O(h)，h 为树高
    pub fn is_valid_bst(root: Option<Rc<RefCell<TreeNode>>>) -> bool {
        let mut stack = Vec::new();
        let mut prev = i64::MIN;   // 记录上一个访问节点的值（使用 i64 避免溢出）
        let mut current = root;

        while !stack.is_empty() || current.is_some() {
            // 将所有左子节点压栈
            while let Some(node) = current {
                stack.push(node.clone());
                current = node.borrow().left.clone();
            }
            // 弹出当前最左节点
            let node = stack.pop().unwrap();
            let val = node.borrow().val as i64;
            if val <= prev {
                return false; // 违反递增性质
            }
            prev = val;
            // 转向右子树
            current = node.borrow().right.clone();
        }
        true
    }
}
```
