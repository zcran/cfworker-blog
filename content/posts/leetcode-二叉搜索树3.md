---
title: "leetcode-二叉搜索树3"
date: 2026-05-14T20:16:14+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 验证二叉搜索树


给你一个二叉树的根节点 root ，判断其是否是一个有效的二叉搜索树。

有效 二叉搜索树定义如下：

节点的左子树只包含 严格小于 当前节点的数。
节点的右子树只包含 严格大于 当前节点的数。
所有左子树和右子树自身必须也是二叉搜索树。

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 验证二叉树是否为有效的二叉搜索树（BST）
    ///
    /// # 原理
    /// 递归检查每个节点是否满足：左子树所有节点值 < 当前节点值 < 右子树所有节点值。
    /// 使用 `i64` 扩大范围，避免节点值为 `i32::MIN` 或 `i32::MAX` 时的比较溢出。
    ///
    /// # 复杂度
    /// - 时间：O(n)，每个节点访问一次
    /// - 空间：O(h)，递归栈深度，h 为树高
    pub fn is_valid_bst(root: Option<Rc<RefCell<TreeNode>>>) -> bool {
        // 递归辅助函数：检查以 `node` 为根的子树是否所有值都在 (min, max) 开区间内
        fn check(node: &Option<Rc<RefCell<TreeNode>>>, min: i64, max: i64) -> bool {
            match node {
                None => true,
                Some(rc) => {
                    let cur = rc.borrow();
                    let val = cur.val as i64;
                    // 当前节点值必须严格在边界内
                    if val <= min || val >= max {
                        return false;
                    }
                    // 递归检查左子树（上限收紧为 val）和右子树（下限收紧为 val）
                    check(&cur.left, min, val) && check(&cur.right, val, max)
                }
            }
        }
        check(&root, i64::MIN, i64::MAX)
    }
}
```
