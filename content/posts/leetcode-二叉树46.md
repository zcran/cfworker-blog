---
title: "leetcode-二叉树46"
date: 2026-08-15T11:32:30+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 从根到叶的二进制数之和

给出一棵二叉树，其上每个结点的值都是 0 或 1 。每一条从根到叶的路径都代表一个从最高有效位开始的二进制数。

例如，如果路径为 0 -> 1 -> 1 -> 0 -> 1，那么它表示二进制数 01101，也就是 13 。

对树上的每一片叶子，我们都要找出从根到该叶子的路径所表示的数字。

返回这些数字之和。题目数据保证答案是一个 32 位 整数。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// DFS 遍历二叉树，维护当前路径的二进制值。
    /// 每深入一层：path = path * 2 + node.val（等价于左移一位）。
    /// 到达叶子节点时，将当前路径值累加到总和。
    pub fn sum_root_to_leaf(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let mut sum = 0;
        Self::dfs(&root, 0, &mut sum);
        sum
    }

    fn dfs(node: &Option<Rc<RefCell<TreeNode>>>, path: i32, sum: &mut i32) {
        let Some(n) = node else { return };
        let n = n.borrow();
        let path = (path << 1) | n.val; // 左移一位，加入当前位

        // 叶子节点：累加路径值
        if n.left.is_none() && n.right.is_none() {
            *sum += path;
            return;
        }

        Self::dfs(&n.left, path, sum);
        Self::dfs(&n.right, path, sum);
    }
}
```
