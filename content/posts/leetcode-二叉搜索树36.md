---
title: "leetcode-二叉搜索树36"
date: 2026-05-14T20:16:16+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 寻找二叉搜索树中的目标节点

某公司组织架构以二叉搜索树形式记录，节点值为处于该职位的员工编号。请返回第 cnt 大的员工编号。

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 返回二叉搜索树中第 k 大的元素（1-indexed）
    /// 采用反向中序遍历（右-根-左），当遍历到第 k 个节点时直接返回
    /// 时间复杂度 O(k + h)，空间复杂度 O(h)，h 为树高
    pub fn find_target_node(root: Option<Rc<RefCell<TreeNode>>>, k: i32) -> i32 {
        let mut count = 0;
        let mut result = 0;
        Self::reverse_inorder(&root, &mut count, &mut result, k);
        result
    }

    fn reverse_inorder(
        node: &Option<Rc<RefCell<TreeNode>>>,
        count: &mut i32,
        result: &mut i32,
        k: i32,
    ) {
        if let Some(rc) = node {
            let n = rc.borrow();
            // 先遍历右子树（更大值）
            Self::reverse_inorder(&n.right, count, result, k);
            // 访问当前节点
            *count += 1;
            if *count == k {
                *result = n.val;
                return;
            }
            // 再遍历左子树（更小值）
            Self::reverse_inorder(&n.left, count, result, k);
        }
    }
}
```
