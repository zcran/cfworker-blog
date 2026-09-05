---
title: "leetcode-二叉树51"
date: 2026-08-15T11:32:30+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 删点成林

给出二叉树的根节点 root，树上每个节点都有一个不同的值。

如果节点值在 to_delete 中出现，我们就把该节点从树上删去，最后得到一个森林（一些不相交的树构成的集合）。

返回森林中的每棵树。你可以按任意顺序组织答案。


```
use std::cell::RefCell;
use std::collections::HashSet;
use std::rc::Rc;

impl Solution {
    /// 后序遍历删除指定节点，被删除节点的子树成为森林中的新树。
    /// 返回森林中所有树的根节点列表。
    pub fn del_nodes(
        root: Option<Rc<RefCell<TreeNode>>>,
        to_delete: Vec<i32>,
    ) -> Vec<Option<Rc<RefCell<TreeNode>>>> {
        let del_set: HashSet<i32> = to_delete.into_iter().collect();
        let mut forest = Vec::new();

        fn dfs(
            node: Option<Rc<RefCell<TreeNode>>>,
            del_set: &HashSet<i32>,
            forest: &mut Vec<Option<Rc<RefCell<TreeNode>>>>,
        ) -> Option<Rc<RefCell<TreeNode>>> {
            let Some(rc) = node else { return None };
            let mut n = rc.borrow_mut();

            // 后序处理左右子树
            n.left = dfs(n.left.take(), del_set, forest);
            n.right = dfs(n.right.take(), del_set, forest);

            if del_set.contains(&n.val) {
                // 当前节点被删，其非空子树成为新的森林根
                let left = n.left.take();
                let right = n.right.take();
                drop(n); // 释放借用，避免阻碍 rc 移动
                if left.is_some() {
                    forest.push(left);
                }
                if right.is_some() {
                    forest.push(right);
                }
                None
            } else {
                drop(n);
                Some(rc) // 直接移动 Rc，避免 clone
            }
        }

        let remaining = dfs(root, &del_set, &mut forest);
        if remaining.is_some() {
            forest.push(remaining);
        }

        forest
    }
}
```
