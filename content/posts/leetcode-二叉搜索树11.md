---
title: "leetcode-二叉搜索树11"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 删除二叉搜索树中的节点


给定一个二叉搜索树的根节点 root 和一个值 key，删除二叉搜索树中的 key 对应的节点，并保证二叉搜索树的性质不变。返回二叉搜索树（有可能被更新）的根节点的引用。

一般来说，删除节点可分为两个步骤：

首先找到需要删除的节点；
如果找到了，删除它。

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 函数式删除：不修改原树，返回新树。
    /// 每次递归沿路径复制节点，未修改的子树共享 Rc。
    pub fn delete_node(
        root: Option<Rc<RefCell<TreeNode>>>,
        key: i32,
    ) -> Option<Rc<RefCell<TreeNode>>> {
        match root {
            None => None,
            Some(node) => {
                let val = node.borrow().val;
                if key < val {
                    // 去左子树删除，右子树直接共享
                    let new_left = Self::delete_node(node.borrow().left.clone(), key);
                    Some(Rc::new(RefCell::new(TreeNode {
                        val,
                        left: new_left,
                        right: node.borrow().right.clone(),
                    })))
                } else if key > val {
                    // 去右子树删除
                    let new_right = Self::delete_node(node.borrow().right.clone(), key);
                    Some(Rc::new(RefCell::new(TreeNode {
                        val,
                        left: node.borrow().left.clone(),
                        right: new_right,
                    })))
                } else {
                    // 删除当前节点
                    let left_child = node.borrow().left.clone();
                    let right_child = node.borrow().right.clone();
                    match (left_child, right_child) {
                        (None, None) => None,
                        (Some(l), None) => Some(l),
                        (None, Some(r)) => Some(r),
                        (Some(l), Some(r)) => {
                            // 右子树的最小值替换当前节点
                            let min_val = Self::find_min(&r);
                            let new_right = Self::delete_node(Some(r), min_val);
                            Some(Rc::new(RefCell::new(TreeNode {
                                val: min_val,
                                left: Some(l),
                                right: new_right,
                            })))
                        }
                    }
                }
            }
        }
    }

    /// 查找树中的最小值（最左节点）—— 迭代版本，无借用冲突
    fn find_min(root: &Rc<RefCell<TreeNode>>) -> i32 {
        let mut cur = Rc::clone(root);
        loop {
            // 每次只短暂借用，获取 left 克隆后立刻释放 Ref
            let left = cur.borrow().left.clone();
            match left {
                Some(l) => cur = l,
                None => break cur.borrow().val,
            }
        }
    }
}
```
