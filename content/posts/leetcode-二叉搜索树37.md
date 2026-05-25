---
title: "leetcode-二叉搜索树37"
date: 2026-05-14T20:16:16+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 最小高度树

给定一个有序整数数组，元素各不相同且按升序排列，编写一个算法，创建一棵高度最小的二叉搜索树。

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn sorted_array_to_bst(nums: Vec<i32>) -> Option<Rc<RefCell<TreeNode>>> {
        // 辅助递归函数：接收切片，返回 Option<节点>
        fn build(slice: &[i32]) -> Option<Rc<RefCell<TreeNode>>> {
            if slice.is_empty() {
                None
            } else {
                let mid = slice.len() / 2;
                let mut node = TreeNode::new(slice[mid]);
                node.left = build(&slice[..mid]);
                node.right = build(&slice[mid + 1..]);
                Some(Rc::new(RefCell::new(node)))
            }
        }
        build(&nums)
    }
}
```
