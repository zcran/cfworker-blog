---
title: "leetcode-二叉搜索树16"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 修剪二叉搜索树

给你二叉搜索树的根节点 root ，同时给定最小边界low 和最大边界 high。通过修剪二叉搜索树，使得所有节点的值在[low, high]中。修剪树 不应该 改变保留在树中的元素的相对结构 (即，如果没有被移除，原有的父代子代关系都应当保留)。 可以证明，存在 唯一的答案 。

所以结果应当返回修剪好的二叉搜索树的新的根节点。注意，根节点可能会根据给定的边界发生改变。

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn trim_bst(root: Option<Rc<RefCell<TreeNode>>>, low: i32, high: i32) -> Option<Rc<RefCell<TreeNode>>> {
        match root {
            None => None,
            Some(node) => {
                let val = node.borrow().val;
                if val < low {
                    // 当前节点及左子树全部小于 low，只需考虑右子树
                    Self::trim_bst(node.borrow().right.clone(), low, high)
                } else if val > high {
                    // 当前节点及右子树全部大于 high，只需考虑左子树
                    Self::trim_bst(node.borrow().left.clone(), low, high)
                } else {
                    // 当前节点在范围内，新建节点（共享未修改的子树）
                    let trimmed_left = Self::trim_bst(node.borrow().left.clone(), low, high);
                    let trimmed_right = Self::trim_bst(node.borrow().right.clone(), low, high);
                    Some(Rc::new(RefCell::new(TreeNode {
                        val,
                        left: trimmed_left,
                        right: trimmed_right,
                    })))
                }
            }
        }
    }
}
```
