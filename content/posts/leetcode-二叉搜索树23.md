---
title: "leetcode-二叉搜索树23"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 从二叉搜索树到更大和树


给定一个二叉搜索树 root (BST)，请将它的每个节点的值替换成树中大于或者等于该节点值的所有节点值之和。

提醒一下， 二叉搜索树 满足下列约束条件：

节点的左子树仅包含键 小于 节点键的节点。
节点的右子树仅包含键 大于 节点键的节点。
左右子树也必须是二叉搜索树。

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 函数式风格：返回新树，原树不变。
    /// 通过反向中序遍历累积和，利用参数传递避免可变状态。
    pub fn bst_to_gst(root: Option<Rc<RefCell<TreeNode>>>) -> Option<Rc<RefCell<TreeNode>>> {
        fn build(node: Option<&Rc<RefCell<TreeNode>>>, acc: i32) -> (Option<Rc<RefCell<TreeNode>>>, i32) {
            match node {
                None => (None, acc),
                Some(node_rc) => {
                    let node_ref = node_rc.borrow();
                    let val = node_ref.val;

                    // 右子树（更大值）
                    let (right_tree, acc_right) = build(node_ref.right.as_ref(), acc);
                    let new_val = acc_right + val;                // 当前节点新值
                    let (left_tree, acc_left) = build(node_ref.left.as_ref(), new_val);

                    let new_node = Rc::new(RefCell::new(TreeNode {
                        val: new_val,
                        left: left_tree,
                        right: right_tree,
                    }));
                    (Some(new_node), acc_left)
                }
            }
        }

        build(root.as_ref(), 0).0
    }
}
```
