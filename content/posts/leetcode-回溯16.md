---
title: "leetcode-回溯16"
date: 2026-07-04T10:22:02+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 路径总和 II

给你二叉树的根节点 root 和一个整数目标和 targetSum ，找出所有 从根节点到叶子节点 路径总和等于给定目标和的路径。

叶子节点 是指没有子节点的节点。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn path_sum(root: Option<Rc<RefCell<TreeNode>>>, target_sum: i32) -> Vec<Vec<i32>> {
        let mut result = Vec::new();
        let mut path = Vec::new();
        Self::dfs(root.as_ref(), target_sum, &mut path, &mut result);
        result
    }

    /// 深度优先搜索所有根到叶子节点的路径
    /// - node: 当前节点
    /// - remaining: 剩余需要累加的目标值
    /// - path: 当前路径
    /// - result: 存储所有符合条件的路径
    fn dfs(
        node: Option<&Rc<RefCell<TreeNode>>>,
        remaining: i32,
        path: &mut Vec<i32>,
        result: &mut Vec<Vec<i32>>,
    ) {
        let Some(node) = node else {
            return;
        };

        let node = node.borrow();
        let val = node.val;

        // 选择当前节点
        path.push(val);

        // 更新剩余目标值
        let new_remaining = remaining - val;

        // 如果是叶子节点且路径和等于目标值
        if node.left.is_none() && node.right.is_none() && new_remaining == 0 {
            result.push(path.clone());
        } else {
            // 继续向下搜索
            Self::dfs(node.left.as_ref(), new_remaining, path, result);
            Self::dfs(node.right.as_ref(), new_remaining, path, result);
        }

        // 回溯：移除当前节点
        path.pop();
    }
}
```
