---
title: "Rust Note 29"
date: 2023-06-04T22:12:07+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 对称二叉树

给你一个二叉树的根节点 root ， 检查它是否轴对称。

### C 解法：
```
 // 递归
class Solution {
public:
    bool check(TreeNode *p, TreeNode *q) {
        if (!p && !q) return true;
        if (!p || !q) return false;
        return p->val == q->val && check(p->left, q->right) && check(p->right, q->left);
    }

    bool isSymmetric(TreeNode* root) {
        return check(root, root);
    }
};
```


### Rust 解法：
```
use std::cell::RefCell;
use std::rc::Rc;
impl Solution {
    fn cmp(left: &Option<Rc<RefCell<TreeNode>>>, right: &Option<Rc<RefCell<TreeNode>>>) -> bool {
        match (&left, &right) {
            (None, None) => true,
            (Some(left), Some(right)) => {
                left.borrow().val == right.borrow().val
                    && Self::cmp(&left.borrow().left, &right.borrow().right)
                    && Self::cmp(&left.borrow().right, &right.borrow().left)
            }
            _ => false,
        }
    }

    pub fn is_symmetric(root: Option<Rc<RefCell<TreeNode>>>) -> bool {
        Self::cmp(&root, &root)
    }
}
```