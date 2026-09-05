---
title: "leetcode-二叉树72"
date: 2026-08-15T11:32:32+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 反转二叉树的奇数层

给你一棵 完美 二叉树的根节点 root ，请你反转这棵树中每个 奇数 层的节点值。

例如，假设第 3 层的节点值是 [2,1,3,4,7,11,29,18] ，那么反转后它应该变成 [18,29,11,7,4,3,1,2] 。

反转后，返回树的根节点。

完美 二叉树需满足：二叉树的所有父节点都有两个子节点，且所有叶子节点都在同一层。

节点的 层数 等于该节点到根节点之间的边数。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn reverse_odd_levels(root: Option<Rc<RefCell<TreeNode>>>) -> Option<Rc<RefCell<TreeNode>>> {
        let mut cur = vec![root.clone().unwrap()];
        let mut is_odd = false;

        while !cur.is_empty() {
            if is_odd {
                // 双指针反转当前奇数层的节点值
                let (mut i, mut j) = (0, cur.len() - 1);
                while i < j {
                    let vi = cur[i].borrow().val;
                    let vj = cur[j].borrow().val;
                    cur[i].borrow_mut().val = vj;
                    cur[j].borrow_mut().val = vi;
                    i += 1;
                    j -= 1;
                }
            }

            // 收集下一层所有节点
            let mut next = Vec::with_capacity(cur.len() * 2);
            for node in &cur {
                let n = node.borrow();
                if let Some(ref l) = n.left {
                    next.push(l.clone());
                }
                if let Some(ref r) = n.right {
                    next.push(r.clone());
                }
            }
            cur = next;
            is_odd = !is_odd;
        }

        root
    }
}
```
