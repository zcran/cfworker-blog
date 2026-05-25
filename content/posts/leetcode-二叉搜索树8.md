---
title: "leetcode-二叉搜索树8"
date: 2026-05-14T20:16:14+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 二叉搜索树中第 K 小的元素


给定一个二叉搜索树的根节点 root ，和一个整数 k ，请你设计一个算法查找其中第 k 小的元素（k 从 1 开始计数）。

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 返回二叉搜索树中第 k 小的元素（k 从 1 开始计数）
    ///
    /// # 原理
    /// 中序遍历 BST，每访问一个节点就减少 k，当 k == 1 时当前节点即为答案。
    /// 使用递归和状态枚举传递剩余计数，避免可变引用。
    ///
    /// # 复杂度
    /// - 时间：O(k) 最坏 O(n)
    /// - 空间：O(h) 递归栈深度，h 为树高
    pub fn kth_smallest(root: Option<Rc<RefCell<TreeNode>>>, k: i32) -> i32 {
        /// 递归状态：
        /// - `Found(i32)`：已经找到第 k 小元素，携带其值
        /// - `Need(i32)`：尚未找到，需要继续遍历，携带剩余需要跳过的节点数
        enum Status {
            Found(i32),
            Need(i32),
        }

        fn dfs(node: &Option<Rc<RefCell<TreeNode>>>, need: i32) -> Status {
            match node {
                None => Status::Need(need),
                Some(rc) => {
                    let node = rc.borrow();
                    // 1. 遍历左子树
                    match dfs(&node.left, need) {
                        Status::Found(val) => return Status::Found(val),
                        Status::Need(left_need) => {
                            // 2. 访问当前节点
                            if left_need == 1 {
                                return Status::Found(node.val);
                            }
                            // 3. 遍历右子树，剩余计数减少 1（已跳过当前节点）
                            dfs(&node.right, left_need - 1)
                        }
                    }
                }
            }
        }

        match dfs(&root, k) {
            Status::Found(val) => val,
            Status::Need(_) => unreachable!("k is valid, so must be found"),
        }
    }
}
```
