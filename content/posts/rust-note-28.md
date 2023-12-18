---
title: "Rust Note 28"
date: 2023-06-04T22:11:41+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 验证二叉搜索树

给你一个二叉树的根节点 root ，判断其是否是一个有效的二叉搜索树。

有效 二叉搜索树定义如下：

节点的左子树只包含 小于 当前节点的数。
节点的右子树只包含 大于 当前节点的数。
所有左子树和右子树自身必须也是二叉搜索树。

### C 解法：
```
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        stack<TreeNode*> stack;
        long long inorder = (long long)INT_MIN - 1;

        while (!stack.empty() || root != nullptr) {
            while (root != nullptr) {
                stack.push(root);
                root = root -> left;
            }
            root = stack.top();
            stack.pop();
            // 如果中序遍历得到的节点的值小于等于前一个 inorder，说明不是二叉搜索树
            if (root -> val <= inorder) {
                return false;
            }
            inorder = root -> val;
            root = root -> right;
        }
        return true;
    }
};
```


### Rust 解法1：
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
use std::cell::RefCell;
use std::rc::Rc;
use std::sync::mpsc::{self, Sender};

impl Solution {
    pub fn is_valid_bst(root: Option<Rc<RefCell<TreeNode>>>) -> bool {
        let (tx, rx) = mpsc::channel();
        let (tx2, rx2) = mpsc::channel();
        fn dfs(node: Option<Rc<RefCell<TreeNode>>>, tx: &Sender<i32>, tx2: &Sender<i32>) {
            if node.is_none() {
                return;
            }
            dfs(node.as_ref().unwrap().borrow().left.clone(), tx, tx2);
            tx.send(node.as_ref().unwrap().borrow().val);
            tx2.send(node.as_ref().unwrap().borrow().val);
            dfs(node.as_ref().unwrap().borrow().right.clone(), tx, tx2);
        }
        dfs(root, &tx, &tx2);
        drop(tx);
        drop(tx2);
        return rx.iter().zip(rx2.iter().skip(1)).all(|(a, b)| a < b);
    }
}
```

### Rust解法2:
```
use std::rc::Rc;
use std::cell::RefCell;
impl Solution {
    pub fn is_valid_bst(root: Option<Rc<RefCell<TreeNode>>>) -> bool {
        fn recv(root: Option<Rc<RefCell<TreeNode>>>, pre: &mut [i64;1]) -> bool {
            if let Some(node) = root {
                if !recv(node.borrow_mut().left.take(), pre) {
                    return false;
                }
                if node.borrow().val as i64 <= pre[0] {
                    return false;
                } else {
                    pre[0] = node.borrow().val as i64;
                }
                recv(node.borrow_mut().right.take(), pre)    
            } else {
                true
            }
        }
        let mut pre = [i64::MIN];
        recv(root, &mut pre)
    }
}
```