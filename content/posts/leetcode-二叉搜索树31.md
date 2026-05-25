---
title: "leetcode-二叉搜索树31"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 二叉搜索树中的中序后继

给定一棵二叉搜索树和其中的一个节点 p ，找到该节点在树中的中序后继。如果节点没有中序后继，请返回 null 。

节点 p 的后继是值比 p.val 大的节点中键值最小的节点，即按中序遍历的顺序节点 p 的下一个节点。

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 寻找 BST 中节点 p 的中序后继（即大于 p 的最小节点）。
    /// 算法：
    /// - 若 p 有右子树，则后继为右子树中最左节点。
    /// - 否则，从根向下搜索，记录最后一个大于 p 的祖先节点。
    pub fn inorder_successor(root: Option<Rc<RefCell<TreeNode>>>, p: Option<Rc<RefCell<TreeNode>>>) -> Option<Rc<RefCell<TreeNode>>> {
        let p_val = p.as_ref()?.borrow().val;
        let mut succ = None;
        let mut cur = root;

        while let Some(rc) = cur {
            let node = rc.borrow();
            if node.val > p_val {
                // 当前节点大于 p，可能是后继，继续向左找更小的可行解
                succ = Some(rc.clone());
                cur = node.left.clone();
            } else {
                // 当前节点 <= p，需要向右找更大的节点
                cur = node.right.clone();
            }
        }
        succ
    }
}
```


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 寻找 BST 中节点 p 的中序后继（大于 p 的最小节点）。
    /// 纯函数式递归实现，利用 BST 性质，空间 O(h)，时间 O(h)。
    pub fn inorder_successor(root: Option<Rc<RefCell<TreeNode>>>, p: Option<Rc<RefCell<TreeNode>>>) -> Option<Rc<RefCell<TreeNode>>> {
        let p_val = p.as_ref()?.borrow().val;

        fn find_succ(node: Option<Rc<RefCell<TreeNode>>>, p_val: i32, succ: Option<Rc<RefCell<TreeNode>>>) -> Option<Rc<RefCell<TreeNode>>> {
            match node {
                None => succ,
                Some(rc) => {
                    // 先借用当前节点，获取左右子树和值，然后立即释放借用
                    let (left, right, val) = {
                        let n = rc.borrow();
                        (n.left.clone(), n.right.clone(), n.val)
                    };
                    if val > p_val {
                        // 当前节点是候选后继，继续向左找更小的可行解
                        find_succ(left, p_val, Some(rc))
                    } else {
                        // 当前节点 <= p，向右搜索，保留原候选后继
                        find_succ(right, p_val, succ)
                    }
                }
            }
        }

        find_succ(root, p_val, None)
    }
}
```
