---
title: "leetcode-回溯20"
date: 2026-07-04T10:22:02+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 二叉树的所有路径

给你一个二叉树的根节点 root ，按 任意顺序 ，返回所有从根节点到叶子节点的路径。

叶子节点 是指没有子节点的节点。




```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn binary_tree_paths(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<String> {
        let mut result = Vec::new();
        Self::dfs(root.as_ref(), String::new(), &mut result);
        result
    }

    /// 深度优先搜索收集所有根到叶子的路径
    /// - node: 当前节点
    /// - path: 从根到当前节点的路径字符串
    /// - result: 存储所有完整路径
    fn dfs(node: Option<&Rc<RefCell<TreeNode>>>, mut path: String, result: &mut Vec<String>) {
        let Some(node) = node else {
            return;
        };

        let node = node.borrow();

        // 将当前节点值追加到路径
        if path.is_empty() {
            path.push_str(&node.val.to_string());
        } else {
            path.push_str(&format!("->{}", node.val));
        }

        // 如果是叶子节点，保存完整路径
        if node.left.is_none() && node.right.is_none() {
            result.push(path);
            return;
        }

        // 继续遍历左右子树
        Self::dfs(node.left.as_ref(), path.clone(), result);
        Self::dfs(node.right.as_ref(), path, result);
    }
}
```
