---
title: "leetcode-二叉树69"
date: 2026-08-15T11:32:32+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 统计值等于子树平均值的节点数

给你一棵二叉树的根节点 root ，找出并返回满足要求的节点数，要求节点的值等于其 子树 中值的 平均值 。

注意：

n 个元素的平均值可以由 n 个元素 求和 然后再除以 n ，并 向下舍入 到最近的整数。
root 的 子树 由 root 和它的所有后代组成。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn average_of_subtree(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        Self::dfs(&root).0
    }

    /// 返回 (满足条件的节点数, 子树节点值之和, 子树节点个数)
    fn dfs(node: &Option<Rc<RefCell<TreeNode>>>) -> (i32, i32, i32) {
        let Some(n) = node else { return (0, 0, 0) };
        let n = n.borrow();

        let (lc, ls, ln) = Self::dfs(&n.left);
        let (rc, rs, rn) = Self::dfs(&n.right);

        let sum = ls + rs + n.val;
        let cnt = ln + rn + 1;
        let is_avg = (sum / cnt) == n.val;

        (lc + rc + is_avg as i32, sum, cnt)
    }
}
```
