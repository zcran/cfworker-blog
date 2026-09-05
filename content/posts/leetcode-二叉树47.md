---
title: "leetcode-二叉树47"
date: 2026-08-15T11:32:30+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 节点与其祖先之间的最大差值

给定二叉树的根节点 root，找出存在于 不同 节点 A 和 B 之间的最大值 V，其中 V = |A.val - B.val|，且 A 是 B 的祖先。

（如果 A 的任何子节点之一为 B，或者 A 的任何子节点是 B 的祖先，那么我们认为 A 是 B 的祖先）


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// DFS 遍历，维护从根到当前节点的路径上的最大值和最小值。
    /// 每访问一个节点，用当前 max-min 更新答案，然后向左右子树递归。
    pub fn max_ancestor_diff(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let Some(root) = root else { return 0 };
        let val = root.borrow().val;
        Self::dfs(&Some(root), val, val)
    }

    fn dfs(node: &Option<Rc<RefCell<TreeNode>>>, mx: i32, mn: i32) -> i32 {
        let Some(n) = node else { return 0 };
        let n = n.borrow();

        let mx = mx.max(n.val);
        let mn = mn.min(n.val);
        let cur = mx - mn;

        // 当前差值 vs 左子树最优 vs 右子树最优
        cur.max(Self::dfs(&n.left, mx, mn))
            .max(Self::dfs(&n.right, mx, mn))
    }
}
```
