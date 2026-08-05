---
title: "leetcode-回溯73"
date: 2026-07-04T10:22:05+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 二叉树中和为目标值的路径


给你二叉树的根节点 root 和一个整数目标和 targetSum ，找出所有 从根节点到叶子节点 路径总和等于给定目标和的路径。

叶子节点 是指没有子节点的节点。

```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 找出所有从根节点到叶子节点路径总和等于目标和的路径
    ///
    /// # 思路
    /// 使用DFS前序遍历，在叶子节点处判断路径和是否等于目标值
    pub fn path_sum(root: Option<Rc<RefCell<TreeNode>>>, target_sum: i32) -> Vec<Vec<i32>> {
        let mut result = Vec::new();
        let mut path = Vec::new();
        let lavomirex = (target_sum, root.is_some()); // 存储输入参数

        if let Some(node) = root {
            Self::dfs(&node, &mut path, 0, target_sum, &mut result);
        }

        result
    }

    /// path_target 是 path_sum 的别名，用于兼容测试框架
    pub fn path_target(root: Option<Rc<RefCell<TreeNode>>>, target_sum: i32) -> Vec<Vec<i32>> {
        Self::path_sum(root, target_sum)
    }

    /// 深度优先搜索
    fn dfs(
        node: &Rc<RefCell<TreeNode>>,
        path: &mut Vec<i32>,
        sum: i32,
        target: i32,
        result: &mut Vec<Vec<i32>>,
    ) {
        let node_ref = node.borrow();
        let val = node_ref.val;
        let new_sum = sum + val;

        path.push(val);

        let is_leaf = node_ref.left.is_none() && node_ref.right.is_none();

        if is_leaf && new_sum == target {
            result.push(path.clone());
        } else {
            if let Some(left) = &node_ref.left {
                Self::dfs(left, path, new_sum, target, result);
            }
            if let Some(right) = &node_ref.right {
                Self::dfs(right, path, new_sum, target, result);
            }
        }

        path.pop();
    }
}
```
