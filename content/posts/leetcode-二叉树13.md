---
title: "leetcode-二叉树13"
date: 2026-08-15T11:32:28+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 完全二叉树的节点个数

给你一棵 完全二叉树 的根节点 root ，求出该树的节点个数。

完全二叉树 的定义如下：在完全二叉树中，除了最底层节点可能没填满外，其余每层节点数都达到最大值，并且最下面一层的节点都集中在该层最左边的若干位置。若最底层为第 h 层（从第 0 层开始），则该层包含 1~ 2^h 个节点。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 返回完全二叉树的节点总数，利用完全二叉树性质 O(log²n) 求解
    pub fn count_nodes(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        let Some(root) = root else { return 0 };

        // 计算左边界深度（沿最左路径走到底）
        let left_depth = Self::depth(&root, true);
        // 计算右边界深度（沿最右路径走到底）
        let right_depth = Self::depth(&root, false);

        // 左右深度相同 → 满二叉树，节点数 = 2^h - 1
        if left_depth == right_depth {
            return (1 << left_depth) - 1;
        }

        // 左右深度不同 → 递归统计左右子树 + 当前节点
        let root = root.borrow();
        1 + Self::count_nodes(root.left.clone()) + Self::count_nodes(root.right.clone())
    }

    /// 沿最左或最右路径计算树高（从当前节点到最深叶子节点的边数）
    fn depth(node: &Rc<RefCell<TreeNode>>, go_left: bool) -> i32 {
        let mut depth = 0;
        let mut cur = Some(node.clone());

        while let Some(node) = cur {
            depth += 1;
            let node = node.borrow();
            cur = if go_left {
                node.left.clone()
            } else {
                node.right.clone()
            };
        }
        depth
    }
}
```
