---
title: "leetcode-二叉树36"
date: 2026-08-15T11:32:30+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 叶子相似的树

请考虑一棵二叉树上所有的叶子，这些叶子的值按从左到右的顺序排列形成一个 叶值序列 。



```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 判断两棵二叉树的叶值序列是否相同
    ///
    /// 分别收集两棵树的叶子节点值，比较是否相等。
    /// 时间复杂度 O(n1 + n2)，空间复杂度 O(l1 + l2)，l 为叶子数
    pub fn leaf_similar(
        root1: Option<Rc<RefCell<TreeNode>>>,
        root2: Option<Rc<RefCell<TreeNode>>>,
    ) -> bool {
        Self::leaf_values(root1) == Self::leaf_values(root2)
    }

    /// 收集二叉树的叶值序列（从左到右）
    fn leaf_values(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
        let mut res = Vec::new();
        Self::dfs(root, &mut res);
        res
    }

    fn dfs(node: Option<Rc<RefCell<TreeNode>>>, res: &mut Vec<i32>) {
        let Some(node) = node else { return };

        let n = node.borrow();
        if n.left.is_none() && n.right.is_none() {
            res.push(n.val);
            return;
        }

        Self::dfs(n.left.clone(), res);
        Self::dfs(n.right.clone(), res);
    }
}
```
