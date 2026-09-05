---
title: "leetcode-二叉树26"
date: 2026-08-15T11:32:29+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 在二叉树中增加一行

给定一个二叉树的根 root 和两个整数 val 和 depth ，在给定的深度 depth 处添加一个值为 val 的节点行。

注意，根节点 root 位于深度 1 。

加法规则如下:

给定整数 depth，对于深度为 depth - 1 的每个非空树节点 cur ，创建两个值为 val 的树节点作为 cur 的左子树根和右子树根。
cur 原来的左子树应该是新的左子树根的左子树。
cur 原来的右子树应该是新的右子树根的右子树。
如果 depth == 1 意味着 depth - 1 根本没有深度，那么创建一个树节点，值 val 作为整个原始树的新根，而原始树就是新根的左子树。


```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 在指定深度插入一行值为 val 的节点
    ///
    /// 深度 1 的特殊情况：新节点作为根，原树挂到左子树。
    /// 其他情况：在 depth-1 层每个非空节点下插入新节点。
    pub fn add_one_row(
        root: Option<Rc<RefCell<TreeNode>>>,
        val: i32,
        depth: i32,
    ) -> Option<Rc<RefCell<TreeNode>>> {
        if depth == 1 {
            return Some(Rc::new(RefCell::new(TreeNode {
                val,
                left: root,
                right: None,
            })));
        }

        Self::dfs(&root, 1, depth, val);
        root
    }

    fn dfs(
        node: &Option<Rc<RefCell<TreeNode>>>,
        cur_depth: i32,
        target: i32,
        val: i32,
    ) {
        let Some(node) = node else { return };
        if cur_depth + 1 == target {
            let mut n = node.borrow_mut();
            let left = n.left.take();
            let right = n.right.take();
            n.left = Some(Rc::new(RefCell::new(TreeNode {
                val,
                left,
                right: None,
            })));
            n.right = Some(Rc::new(RefCell::new(TreeNode {
                val,
                left: None,
                right,
            })));
            return;
        }

        let n = node.borrow();
        Self::dfs(&n.left, cur_depth + 1, target, val);
        Self::dfs(&n.right, cur_depth + 1, target, val);
    }
}
```
