---
title: "Rust Note 27"
date: 2023-06-04T22:11:25+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 二叉树的最大深度

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

说明: 叶子节点是指没有子节点的节点。

### C 解法：
```
 // 深度优先，递归
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (root == nullptr) return 0;
        return max(maxDepth(root->left), maxDepth(root->right)) + 1;
    }
};
```


### Rust 解法：
```
// Definition for a binary tree node.
// #[derive(Debug, PartialEq, Eq)]
// pub struct TreeNode {
//   pub val: i32,
//   pub left: Option<Rc<RefCell<TreeNode>>>,
//   pub right: Option<Rc<RefCell<TreeNode>>>,
// }
//
// impl TreeNode {
//   #[inline]
//   pub fn new(val: i32) -> Self {
//     TreeNode {
//       val,
//       left: None,
//       right: None
//     }
//   }
// }
use std::rc::Rc;
use std::cell::RefCell;
impl Solution {
    pub fn max_depth(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
        Self::max_depth_traverse_impl(&root, 1)
    }

    pub fn max_depth_traverse_impl(root: &Option<Rc<RefCell<TreeNode>>>, mut depth: i32) -> i32 {
        use std::cmp::Ordering;
        if let Some(node) = root {
            let (left, right) = (node.borrow().left.clone(), node.borrow().right.clone());
            return match (left.clone(), right.clone()) {
                (None, None) => depth,
                (None, Some((right))) => {
                    Self::max_depth_traverse_impl(&Some(right), depth + 1)
                }
                (Some(left), None) => {
                    Self::max_depth_traverse_impl(&Some(left), depth + 1)
                }
                (Some(left), Some(right)) => {
                    let left_depth = Self::max_depth_traverse_impl(&Some(left), depth + 1);
                    let right_depth = Self::max_depth_traverse_impl(&Some(right), depth + 1);
                    match left_depth.cmp(&right_depth) {
                        Ordering::Equal => {
                            left_depth
                        }
                        Ordering::Less => {
                            right_depth
                        }
                        _ => {
                            left_depth
                        }
                    }
                }
            };
        }
        depth - 1
    }

}
```