---
title: "leetcode-字符串匹配3"
date: 2026-04-20T20:32:10+08:00
tags: ["leetcode", "字符串匹配"]
draft: false
---


## 另一棵树的子树

给你两棵二叉树 root 和 subRoot 。检验 root 中是否包含和 subRoot 具有相同结构和节点值的子树。如果存在，返回 true ；否则，返回 false 。

二叉树 tree 的一棵子树包括 tree 的某个节点和这个节点的所有后代节点。tree 也可以看做它自身的一棵子树。


```
// Definition for a binary tree node.
// #[derive(Debug, PartialEq, Eq)]
// pub struct TreeNode {
//   pub val: i32,
//   pub left: Option<Rc<RefCell<TreeNode>>>,
//   pub right: Option<Rc<RefCell<TreeNode>>>,
// }
//
// impl TreeNode {
//   #[inline]
//   pub fn new(val: i32) -> Self {
//     TreeNode {
//       val,
//       left: None,
//       right: None
//     }
//   }
// }

use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 判断 `sub_root` 是否为 `root` 的子树。
    ///
    /// # 算法思路
    /// 对主树进行深度优先遍历（DFS）。
    /// 在每一个节点处，检查以该节点为根的子树是否与 `sub_root` 完全相同。
    /// 若相同，则立即返回 `true`；否则，继续在左、右子树中递归查找。
    ///
    /// # 复杂度分析
    /// - **时间复杂度**：O(|root| × |sub_root|)。
    ///   最坏情况下，主树的每个节点都会触发一次完整的 `is_same_tree` 比较。
    /// - **空间复杂度**：O(height)。
    ///   递归调用栈的深度取决于主树的高度。
    pub fn is_subtree(
        root: Option<Rc<RefCell<TreeNode>>>,
        sub_root: Option<Rc<RefCell<TreeNode>>>,
    ) -> bool {
        // 使用 `Option::as_ref` 配合 `map_or` 来优雅地处理空节点情况。
        // 若当前节点为 None，则直接返回 false（空树不可能包含非空子树）。
        root.as_ref().map_or(false, |root_rc| {
            // 核心逻辑：
            // 1. 首先判断以当前 `root` 节点为根的树是否与 `sub_root` 完全相同。
            // 2. 若不同，则递归检查左子树是否包含 `sub_root`。
            // 3. 若仍不同，则递归检查右子树是否包含 `sub_root`。
            // 注意：`Rc::clone` 只会增加强引用计数，代价极低，无需担心性能问题。
            Self::is_same_tree(Some(root_rc.clone()), sub_root.clone())
                || Self::is_subtree(root_rc.borrow().left.clone(), sub_root.clone())
                || Self::is_subtree(root_rc.borrow().right.clone(), sub_root.clone())
        })
    }

    /// 判断两棵二叉树是否在**结构**和**节点值**上完全一致。
    ///
    /// # 参数
    /// - `p`: 第一棵树的根节点（Option 包装）。
    /// - `q`: 第二棵树的根节点（Option 包装）。
    ///
    /// # 返回值
    /// 若两棵树完全相同返回 `true`，否则返回 `false`。
    fn is_same_tree(
        p: Option<Rc<RefCell<TreeNode>>>,
        q: Option<Rc<RefCell<TreeNode>>>,
    ) -> bool {
        // 利用模式匹配同时解构两个 Option，逻辑清晰且无 unwrap 风险。
        match (p, q) {
            // 情况 1：两棵树均为空节点 -> 它们相同。
            (None, None) => true,

            // 情况 2：两棵树均非空 -> 需要进一步比较节点值以及左、右子树。
            (Some(p_rc), Some(q_rc)) => {
                // 通过 `borrow()` 获取内部 TreeNode 的不可变引用。
                let p_ref = p_rc.borrow();
                let q_ref = q_rc.borrow();

                // 当前节点值必须相等，
                // 并且左子树完全相同，
                // 并且右子树完全相同。
                p_ref.val == q_ref.val
                    && Self::is_same_tree(p_ref.left.clone(), q_ref.left.clone())
                    && Self::is_same_tree(p_ref.right.clone(), q_ref.right.clone())
            }

            // 情况 3：一棵树为空，另一棵非空 -> 结构不同，返回 false。
            _ => false,
        }
    }
}
```
