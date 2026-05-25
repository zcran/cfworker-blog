---
title: "leetcode-二叉搜索树9"
date: 2026-05-14T20:16:14+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---



##  二叉搜索树的最近公共祖先

给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 返回二叉搜索树中节点 p 和 q 的最低公共祖先（LCA）
    ///
    /// # 原理
    /// 利用 BST 性质：对于当前节点 root，
    /// - 若 p 和 q 的值都小于 root，则 LCA 在左子树；
    /// - 若都大于 root，则 LCA 在右子树；
    /// - 否则（一个在左一个在右，或其中一个等于 root），root 即为 LCA。
    ///
    /// # 复杂度
    /// - 时间：O(h)，h 为树高
    /// - 空间：O(h)，递归栈深度
    pub fn lowest_common_ancestor(
        root: Option<Rc<RefCell<TreeNode>>>,
        p: Option<Rc<RefCell<TreeNode>>>,
        q: Option<Rc<RefCell<TreeNode>>>,
    ) -> Option<Rc<RefCell<TreeNode>>> {
        // 安全解包：题目保证 root、p、q 均为 Some
        let root_node = root.as_ref()?;
        let root_val = root_node.borrow().val;
        let p_val = p.as_ref()?.borrow().val;
        let q_val = q.as_ref()?.borrow().val;

        if p_val < root_val && q_val < root_val {
            // 都在左子树：递归左子树
            Self::lowest_common_ancestor(root_node.borrow().left.clone(), p, q)
        } else if p_val > root_val && q_val > root_val {
            // 都在右子树：递归右子树
            Self::lowest_common_ancestor(root_node.borrow().right.clone(), p, q)
        } else {
            // 当前节点即为 LCA
            root
        }
    }
}
```
