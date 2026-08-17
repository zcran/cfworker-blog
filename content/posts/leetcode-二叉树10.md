---
title: "leetcode-二叉树10"
date: 2026-08-15T11:32:28+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树中的最大路径和

二叉树中的 路径 被定义为一条节点序列，序列中每对相邻节点之间都存在一条边。同一个节点在一条路径序列中 至多出现一次 。该路径 至少包含一个 节点，且不一定经过根节点。

路径和 是路径中各节点值的总和。

给你一个二叉树的根节点 root ，返回其 最大路径和 。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 返回二叉树中任意路径的最大路径和（路径不重复经过节点，至少包含一个节点）
    pub fn max_path_sum(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let mut ans = i32::MIN;
        Self::dfs(&root, &mut ans);
        ans
    }

    /// 返回以当前节点为端点的最大链和，同时更新全局最大路径和
    fn dfs(node: &Option<Rc<RefCell<TreeNode>>>, ans: &mut i32) -> i32 {
        if let Some(node) = node {
            let node = node.borrow();
            let left = Self::dfs(&node.left, ans);
            let right = Self::dfs(&node.right, ans);
            // 经过当前节点的完整路径 = 左链 + 当前值 + 右链
            *ans = (*ans).max(left + node.val + right);
            // 向上延伸的最大链和 = 当前值 + max(左链, 右链, 0)
            0.max(left.max(right) + node.val)
        } else {
            0
        }
    }
}
```
