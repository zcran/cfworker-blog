---
title: "leetcode-二叉树12"
date: 2026-08-15T11:32:28+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树的右视图

给定一个二叉树的 根节点 root，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 返回二叉树右视图：从右侧能看到的节点值（从上到下）
    pub fn right_side_view(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
        let mut ans = vec![];
        Self::dfs(&root, 0, &mut ans);
        ans
    }

    /// 先右后左 DFS：每层第一个访问到的节点即为最右侧可见节点
    fn dfs(node: &Option<Rc<RefCell<TreeNode>>>, depth: usize, ans: &mut Vec<i32>) {
        if let Some(node) = node {
            let node = node.borrow();
            // 当前深度首次遇到节点 → 即该层最右节点
            if depth == ans.len() {
                ans.push(node.val);
            }
            Self::dfs(&node.right, depth + 1, ans);
            Self::dfs(&node.left, depth + 1, ans);
        }
    }
}
```
