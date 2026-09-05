---
title: "leetcode-二叉树77"
date: 2026-08-15T11:32:32+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 二叉树的堂兄弟节点 II

给你一棵二叉树的根 root ，请你将每个节点的值替换成该节点的所有 堂兄弟节点值的和 。

如果两个节点在树中有相同的深度且它们的父节点不同，那么它们互为 堂兄弟 。

请你返回修改值之后，树的根 root 。

注意，一个节点的深度指的是从树根节点到这个节点经过的边数。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn replace_value_in_tree(root: Option<Rc<RefCell<TreeNode>>>) -> Option<Rc<RefCell<TreeNode>>> {
        let mut q = vec![root.clone().unwrap()];
        // 根节点无堂兄弟，值设为 0
        q[0].borrow_mut().val = 0;

        while !q.is_empty() {
            // 第一遍：收集下一层所有节点，并计算下一层总和
            let mut next = Vec::with_capacity(q.len() * 2);
            let mut level_sum = 0i32;

            for node in &q {
                let n = node.borrow();
                if let Some(ref l) = n.left {
                    next.push(l.clone());
                    level_sum += l.borrow().val;
                }
                if let Some(ref r) = n.right {
                    next.push(r.clone());
                    level_sum += r.borrow().val;
                }
            }

            // 第二遍：每个节点的子节点值 = 下一层总和 - 该节点子节点值之和
            for node in &q {
                let n = node.borrow();
                let sibling_sum = n.left.as_ref().map_or(0, |l| l.borrow().val)
                    + n.right.as_ref().map_or(0, |r| r.borrow().val);

                if let Some(ref l) = n.left {
                    l.borrow_mut().val = level_sum - sibling_sum;
                }
                if let Some(ref r) = n.right {
                    r.borrow_mut().val = level_sum - sibling_sum;
                }
            }

            q = next;
        }

        root
    }
}
```
