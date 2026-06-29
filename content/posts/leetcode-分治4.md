---
title: "leetcode-分治4"
date: 2026-06-13T10:51:38+08:00
tags: ["leetcode", "分治"]
draft: false
---

## 从中序与后序遍历序列构造二叉树

给定两个整数数组 inorder 和 postorder ，其中 inorder 是二叉树的中序遍历， postorder 是同一棵树的后序遍历，请你构造并返回这颗 二叉树 。


```
use std::cell::RefCell;
use std::collections::HashMap;
use std::rc::Rc;

impl Solution {
    /// 从中序遍历和后序遍历构建二叉树
    ///
    /// # 算法原理
    /// - 后序遍历：`[左子树..., 右子树..., 根]`
    /// - 中序遍历：`[左子树..., 根, 右子树...]`
    /// - 利用哈希表快速定位根节点在中序遍历中的位置
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n)，每个节点处理一次
    /// - 空间复杂度：O(n)，哈希表 + 递归栈
    pub fn build_tree(inorder: Vec<i32>, postorder: Vec<i32>) -> Option<Rc<RefCell<TreeNode>>> {
        let n = inorder.len();
        // 建立中序遍历的值→索引映射，实现 O(1) 查找
        let in_pos = inorder
            .into_iter()
            .enumerate()
            .map(|(i, v)| (v, i))
            .collect::<HashMap<_, _>>();

        Self::build_recursive(&postorder, &in_pos, 0, n, 0, n)
    }

    /// 递归构建子树
    ///
    /// # 参数说明
    /// - `postorder`: 后序遍历数组
    /// - `in_pos`: 中序遍历的值→索引映射
    /// - `post_l`, `post_r`: 后序遍历区间 [post_l, post_r) (左闭右开)
    /// - `in_l`, `in_r`: 中序遍历区间 [in_l, in_r) (左闭右开)
    fn build_recursive(
        postorder: &[i32],
        in_pos: &HashMap<i32, usize>,
        post_l: usize,
        post_r: usize,
        in_l: usize,
        in_r: usize,
    ) -> Option<Rc<RefCell<TreeNode>>> {
        if post_l >= post_r || in_l >= in_r {
            return None;
        }

        // 后序遍历的最后一个元素是根节点
        let root_val = postorder[post_r - 1];
        let root = Rc::new(RefCell::new(TreeNode::new(root_val)));

        // 在中序遍历中找到根的位置，划分左右子树
        let root_idx = *in_pos.get(&root_val).unwrap();
        let left_size = root_idx - in_l;

        // 递归构建左子树
        let left = Self::build_recursive(
            postorder,
            in_pos,
            post_l,                    // 左子树后序起点
            post_l + left_size,        // 左子树后序终点（不含）
            in_l,                      // 左子树中序起点
            root_idx,                  // 左子树中序终点（不含）
        );

        // 递归构建右子树
        let right = Self::build_recursive(
            postorder,
            in_pos,
            post_l + left_size,        // 右子树后序起点
            post_r - 1,                // 右子树后序终点（不含，排除根）
            root_idx + 1,              // 右子树中序起点
            in_r,                      // 右子树中序终点（不含）
        );

        root.borrow_mut().left = left;
        root.borrow_mut().right = right;
        Some(root)
    }
}
```
