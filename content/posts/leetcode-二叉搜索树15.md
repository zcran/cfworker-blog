---
title: "leetcode-二叉搜索树15"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 两数之和 IV - 输入二叉搜索树


给定一个二叉搜索树 root 和一个目标结果 k，如果二叉搜索树中存在两个元素且它们的和等于给定的目标结果，则返回 true。

```
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::HashSet;

impl Solution {
    pub fn find_target(root: Option<Rc<RefCell<TreeNode>>>, k: i32) -> bool {
        let mut need = HashSet::new();

        fn dfs(node: Option<Rc<RefCell<TreeNode>>>, need: &mut HashSet<i32>, k: i32) -> bool {
            if let Some(n) = node {
                let n_ref = n.borrow();
                let val = n_ref.val;
                if need.contains(&val) {
                    return true;
                }
                need.insert(k - val);
                dfs(n_ref.left.clone(), need, k) || dfs(n_ref.right.clone(), need, k)
            } else {
                false
            }
        }

        dfs(root, &mut need, k)
    }
}
```
