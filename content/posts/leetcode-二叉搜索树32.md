---
title: "leetcode-二叉搜索树32"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 把二叉搜索树转换为累加树

给定一个二叉搜索树，请将它的每个节点的值替换成树中大于或者等于该节点值的所有节点值之和。



提醒一下，二叉搜索树满足下列约束条件：

节点的左子树仅包含键 小于 节点键的节点。
节点的右子树仅包含键 大于 节点键的节点。
左右子树也必须是二叉搜索树。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 将二叉搜索树转换为累加树（Greater Tree）
    /// 采用反向中序遍历（右-根-左），累加节点值并更新
    pub fn convert_bst(root: Option<Rc<RefCell<TreeNode>>>) -> Option<Rc<RefCell<TreeNode>>> {
        Self::gst(&root, &mut 0);
        root
    }

    /// 递归辅助函数：反向中序遍历，同时累加和
    /// - `node`: 当前节点引用
    /// - `sum`: 累加和（可变引用，记录已遍历的较大节点值之和）
    fn gst(node: &Option<Rc<RefCell<TreeNode>>>, sum: &mut i32) {
        if let Some(rc) = node {
            // 一次性获取可变借用，避免多次调用 borrow_mut
            let mut n = rc.borrow_mut();

            // 1. 遍历右子树（所有更大节点）
            Self::gst(&n.right, sum);
            // 2. 更新当前节点值：原值 + 右侧累计和
            *sum += n.val;
            n.val = *sum;
            // 3. 遍历左子树（此时左子树所有节点都会加上当前的累计和）
            Self::gst(&n.left, sum);
        }
    }
}
```
