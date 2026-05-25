---
title: "leetcode-二叉搜索树35"
date: 2026-05-14T20:16:16+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 验证二叉搜索树的后序遍历序列

请实现一个函数来判断整数数组 postorder 是否为二叉搜索树的后序遍历结果。


```
impl Solution {
    /// 验证是否为二叉搜索树的后序遍历序列
    /// 使用单调栈从右向左扫描，维护一个递增的“根”边界
    pub fn verify_tree_order(postorder: Vec<i32>) -> bool {
        let mut stack = Vec::new();
        let mut parent = i32::MAX;   // 当前子树的父节点值（上界）

        for &val in postorder.iter().rev() {
            // 如果当前节点大于父节点，说明违反了 BST 左子树 ≤ 根的性质
            if val > parent {
                return false;
            }
            // 当栈非空且当前节点小于栈顶时，说明进入了某个节点的左子树
            // 弹出所有比当前节点大的栈顶元素，最后一个弹出的即为新的父节点边界
            while let Some(&top) = stack.last() {
                if top <= val {
                    break;
                }
                parent = stack.pop().unwrap();
            }
            stack.push(val);
        }
        true
    }
}
```
