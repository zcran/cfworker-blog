---
title: "leetcode-二叉树21"
date: 2026-08-15T11:32:29+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 在每个树行中找最大值

给定一棵二叉树的根节点 root ，请找出该二叉树中每一层的最大值。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 返回二叉树每一层的最大值（DFS 先序遍历）
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(h)，h 为树高
    pub fn largest_values(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
        let mut ans = Vec::new();
        Self::dfs(root, 0, &mut ans);
        ans
    }

    fn dfs(node: Option<Rc<RefCell<TreeNode>>>, depth: usize, ans: &mut Vec<i32>) {
        let Some(node) = node else { return };

        let val = node.borrow().val;
        if depth == ans.len() {
            ans.push(val);
        } else {
            ans[depth] = ans[depth].max(val);
        }

        let left = node.borrow().left.clone();
        let right = node.borrow().right.clone();

        Self::dfs(left, depth + 1, ans);
        Self::dfs(right, depth + 1, ans);
    }
}
```
