---
title: "leetcode-树28"
date: 2026-09-24T09:47:34+08:00
tags: ["leetcode", "树"]
draft: false
---


## 子结构判断

给定两棵二叉树 tree1 和 tree2，判断 tree2 是否以 tree1 的某个节点为根的子树具有 相同的结构和节点值 。
注意，空树 不会是以 tree1 的某个节点为根的子树具有 相同的结构和节点值 。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 判断树 B 是否是树 A 的子结构。
    ///
    /// 思路：
    /// 1. 遍历 A 的每个节点，对每个节点调用 `is_sub` 判断以该节点为根的子树
    ///    是否与 B 完全匹配。
    /// 2. `is_sub(a, b)`：判断以 a 和 b 为根的两棵子树是否结构和值完全一致。
    ///    - 值不同 -> false
    ///    - b 为叶子 -> true
    ///    - b 有子节点但 a 对应位置缺失 -> false
    ///    - 递归比较左右子树
    ///
    /// 优化：
    /// - 使用迭代遍历 A，避免递归深度过大。
    /// - `is_sub` 保持递归（子结构深度通常较小）。
    /// - 避免不必要的 `Rc::clone`，改用借用和 `as_ref()`。
    pub fn is_sub_structure(
        a: Option<Rc<RefCell<TreeNode>>>,
        b: Option<Rc<RefCell<TreeNode>>>,
    ) -> bool {
        // 空树不是任何树的子结构
        let (a, b) = match (a, b) {
            (Some(a), Some(b)) => (a, b),
            _ => return false,
        };

        /// 判断以 a 和 b 为根的两棵子树是否完全一致
        fn is_sub(a: &Rc<RefCell<TreeNode>>, b: &Rc<RefCell<TreeNode>>) -> bool {
            let a_borrow = a.borrow();
            let b_borrow = b.borrow();

            if a_borrow.val != b_borrow.val {
                return false;
            }

            // b 的当前节点没有子节点，说明 b 的这一分支已匹配完
            if b_borrow.left.is_none() && b_borrow.right.is_none() {
                return true;
            }

            // 检查左子树
            let left_ok = match (&a_borrow.left, &b_borrow.left) {
                (_, None) => true,
                (Some(a_left), Some(b_left)) => is_sub(a_left, b_left),
                (None, Some(_)) => false,
            };

            // 检查右子树
            let right_ok = match (&a_borrow.right, &b_borrow.right) {
                (_, None) => true,
                (Some(a_right), Some(b_right)) => is_sub(a_right, b_right),
                (None, Some(_)) => false,
            };

            left_ok && right_ok
        }

        // 迭代遍历 A 的每个节点，检查是否与 B 匹配
        let mut stack = vec![a];
        while let Some(node) = stack.pop() {
            if is_sub(&node, &b) {
                return true;
            }
            let node_borrow = node.borrow();
            if let Some(left) = &node_borrow.left {
                stack.push(Rc::clone(left));
            }
            if let Some(right) = &node_borrow.right {
                stack.push(Rc::clone(right));
            }
        }

        false
    }
}
```
