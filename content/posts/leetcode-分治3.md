---
title: "leetcode-分治3"
date: 2026-06-13T10:51:38+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 从前序与中序遍历序列构造二叉树

给定两个整数数组 preorder 和 inorder ，其中 preorder 是二叉树的先序遍历， inorder 是同一棵树的中序遍历，请构造二叉树并返回其根节点。

```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::HashMap;

impl Solution {
    /// 从前序遍历和中序遍历构建二叉树
    ///
    /// # 算法原理
    /// - 前序遍历：`[根, 左子树..., 右子树...]`
    /// - 中序遍历：`[左子树..., 根, 右子树...]`
    /// - 利用哈希表加速根节点在中序遍历中的查找
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n)，每个节点处理一次
    /// - 空间复杂度：O(n)，哈希表 + 递归栈
    pub fn build_tree(preorder: Vec<i32>, inorder: Vec<i32>) -> Option<Rc<RefCell<TreeNode>>> {
        let n = preorder.len();
        // 建立值到索引的映射，O(1) 时间定位根节点
        let in_pos = inorder
            .into_iter()
            .enumerate()
            .map(|(i, v)| (v, i))
            .collect::<HashMap<_, _>>();

        Self::build_recursive(&preorder, &in_pos, 0, n, 0, n)
    }

    /// 递归构建子树
    ///
    /// # 参数说明
    /// - `preorder`: 前序遍历数组
    /// - `in_pos`: 中序遍历的值->索引映射
    /// - `pre_l`, `pre_r`: 前序遍历区间 [pre_l, pre_r) (左闭右开)
    /// - `in_l`, `in_r`: 中序遍历区间 [in_l, in_r) (左闭右开)
    fn build_recursive(
        preorder: &[i32],
        in_pos: &HashMap<i32, usize>,
        pre_l: usize,
        pre_r: usize,
        in_l: usize,
        in_r: usize,
    ) -> Option<Rc<RefCell<TreeNode>>> {
        if pre_l >= pre_r || in_l >= in_r {
            return None;
        }

        // 根节点值为前序遍历的第一个元素
        let root_val = preorder[pre_l];
        let root = Rc::new(RefCell::new(TreeNode::new(root_val)));

        // 在中序遍历中找到根的位置，划分左右子树
        let root_idx = *in_pos.get(&root_val).unwrap();
        let left_size = root_idx - in_l;

        // 递归构建：左子树区间和右子树区间
        let left = Self::build_recursive(
            preorder,
            in_pos,
            pre_l + 1,           // 左子树前序起点
            pre_l + 1 + left_size, // 左子树前序终点（不含）
            in_l,                // 左子树中序起点
            root_idx,            // 左子树中序终点（不含）
        );

        let right = Self::build_recursive(
            preorder,
            in_pos,
            pre_l + 1 + left_size, // 右子树前序起点
            pre_r,                // 右子树前序终点（不含）
            root_idx + 1,         // 右子树中序起点
            in_r,                 // 右子树中序终点（不含）
        );

        root.borrow_mut().left = left;
        root.borrow_mut().right = right;
        Some(root)
    }
}
```
