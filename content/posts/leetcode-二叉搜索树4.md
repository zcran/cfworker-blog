---
title: "leetcode-二叉搜索树4"
date: 2026-05-14T20:16:14+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---



## 恢复二叉搜索树


给你二叉搜索树的根节点 root ，该树中的 恰好 两个节点的值被错误地交换。请在不改变其结构的情况下，恢复这棵树 。

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 恢复二叉搜索树：树中恰好有两个节点的值被错误交换，请在不改变结构的前提下恢复这棵树。
    ///
    /// # 原理
    /// 中序遍历 BST 应得到升序序列。若出现逆序对，则说明存在错误节点。
    /// 使用递归中序遍历，维护前驱节点 `prev`，当 `prev.val > cur.val` 时记录错误节点。
    /// 遍历完成后交换两个错误节点的值。
    ///
    /// # 复杂度
    /// - 时间：O(n)，每个节点访问一次
    /// - 空间：O(h)，递归栈深度，最坏 O(n)
    pub fn recover_tree(root: &mut Option<Rc<RefCell<TreeNode>>>) {
        // 存放需要交换的两个节点（直接持有 Rc，便于修改内部值）
        let mut first: Option<Rc<RefCell<TreeNode>>> = None;
        let mut second: Option<Rc<RefCell<TreeNode>>> = None;
        let mut prev: Option<Rc<RefCell<TreeNode>>> = None;

        // 中序遍历闭包（函数式递归，无外部可变状态，仅通过引用修改捕获变量）
        fn inorder(
            node: Option<&Rc<RefCell<TreeNode>>>,
            first: &mut Option<Rc<RefCell<TreeNode>>>,
            second: &mut Option<Rc<RefCell<TreeNode>>>,
            prev: &mut Option<Rc<RefCell<TreeNode>>>,
        ) {
            if let Some(n) = node {
                let n_ref = n.borrow();
                // 左子树
                inorder(n_ref.left.as_ref(), first, second, prev);

                // 当前节点与前驱比较
                if let Some(p) = prev {
                    if p.borrow().val > n_ref.val {
                        if first.is_none() {
                            *first = Some(p.clone());   // 第一个错误节点是前驱
                        }
                        *second = Some(n.clone());      // 第二个错误节点是当前节点
                    }
                }
                *prev = Some(n.clone()); // 更新前驱

                // 右子树
                inorder(n_ref.right.as_ref(), first, second, prev);
            }
        }

        inorder(root.as_ref(), &mut first, &mut second, &mut prev);

        // 交换两个节点的值（无需修改树结构）
        if let (Some(f), Some(s)) = (first, second) {
            let f_val = f.borrow().val;
            let s_val = s.borrow().val;
            f.borrow_mut().val = s_val;
            s.borrow_mut().val = f_val;
        }
    }
}
```
