---
title: "leetcode-二叉树90"
date: 2026-08-15T11:32:33+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 求根节点到叶节点数字之和

给定一个二叉树的根节点 root ，树中每个节点都存放有一个 0 到 9 之间的数字。

每条从根节点到叶节点的路径都代表一个数字：

例如，从根节点到叶节点的路径 1 -> 2 -> 3 表示数字 123 。

计算从根节点到叶节点生成的 所有数字之和 。

叶节点 是指没有子节点的节点。

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 返回从根到叶所有路径表示的数字之和。
    ///
    /// 深度优先遍历，维护当前路径值 `path`。
    /// 到达叶节点时，将 `path` 累加到总和中。
    pub fn sum_numbers(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        Self::dfs(root, 0)
    }

    fn dfs(node: Option<Rc<RefCell<TreeNode>>>, path: i32) -> i32 {
        let Some(n) = node else { return 0 };

        let n = n.borrow();
        let path = path * 10 + n.val;

        // 叶节点：返回当前路径值
        if n.left.is_none() && n.right.is_none() {
            return path;
        }

        // 非叶节点：递归左右子树，累加结果
        Self::dfs(n.left.clone(), path) + Self::dfs(n.right.clone(), path)
    }
}
```
