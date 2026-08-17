---
title: "leetcode-二叉树16"
date: 2026-08-15T11:32:28+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 打家劫舍 III

小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为 root 。

除了 root 之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果 两个直接相连的房子在同一天晚上被打劫 ，房屋将自动报警。

给定二叉树的 root 。返回 在不触动警报的情况下 ，小偷能够盗取的最高金额 。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 打家劫舍 III：二叉树版，相邻节点不能同时选，返回最大金额
    pub fn rob(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let (rob_root, skip_root) = Self::dfs(&root);
        rob_root.max(skip_root)
    }

    /// 返回 (选当前节点的最大金额, 不选当前节点的最大金额)
    fn dfs(node: &Option<Rc<RefCell<TreeNode>>>) -> (i32, i32) {
        let node = match node {
            Some(n) => n.borrow(),
            None => return (0, 0),
        };

        let (l_rob, l_skip) = Self::dfs(&node.left);
        let (r_rob, r_skip) = Self::dfs(&node.right);

        // 选当前节点：左右子节点都不能选
        let rob = node.val + l_skip + r_skip;
        // 不选当前节点：左右子节点可选可不选，取各自最优
        let skip = l_rob.max(l_skip) + r_rob.max(r_skip);

        (rob, skip)
    }
}
```
