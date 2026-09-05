---
title: "leetcode-二叉树37"
date: 2026-08-15T11:32:30+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 翻转等价二叉树

我们可以为二叉树 T 定义一个 翻转操作 ，如下所示：选择任意节点，然后交换它的左子树和右子树。

只要经过一定次数的翻转操作后，能使 X 等于 Y，我们就称二叉树 X 翻转 等价 于二叉树 Y。

这些树由根节点 root1 和 root2 给出。如果两个二叉树是翻转 等价 的树，则返回 true ，否则返回 false 。




```
use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    /// 判断两棵二叉树是否翻转等价
    ///
    /// 两棵树翻转等价当且仅当：根值相同，且（不翻转时子树对应等价）或（翻转后子树交叉等价）。
    /// 时间复杂度 O(min(n1, n2))，空间复杂度 O(h)
    pub fn flip_equiv(
        root1: Option<Rc<RefCell<TreeNode>>>,
        root2: Option<Rc<RefCell<TreeNode>>>,
    ) -> bool {
        match (root1, root2) {
            (None, None) => true,
            (Some(n1), Some(n2)) => {
                let (t1, t2) = (n1.borrow(), n2.borrow());
                t1.val == t2.val
                    && ((Self::flip_equiv(t1.left.clone(), t2.left.clone())
                        && Self::flip_equiv(t1.right.clone(), t2.right.clone()))
                        || (Self::flip_equiv(t1.left.clone(), t2.right.clone())
                            && Self::flip_equiv(t1.right.clone(), t2.left.clone())))
            }
            _ => false,
        }
    }
}
```
