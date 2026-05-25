---
title: "leetcode-二叉搜索树14"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 把二叉搜索树转换为累加树


给出二叉 搜索 树的根节点 root，该树的节点值各不相同，请你将其转换为累加树（Greater Sum Tree），将其转换为一个更大的树，使得原始二叉搜索树中的每个节点值都变为原本值加上原本二叉搜索树中所有比该节点值大的节点值的总和。

提醒一下，二叉搜索树满足下列约束条件：

节点的左子树仅包含键 小于 节点键的节点。
节点的右子树仅包含键 大于 节点键的节点。
左右子树也必须是二叉搜索树。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn convert_bst(root: Option<Rc<RefCell<TreeNode>>>) -> Option<Rc<RefCell<TreeNode>>> {
        // 递归函数：返回 (新子树根, 累计和)
        fn convert(
            node: Option<Rc<RefCell<TreeNode>>>,
            acc: i32,  // 从父节点传递的累积和（右子树的总和）
        ) -> (Option<Rc<RefCell<TreeNode>>>, i32) {
            match node {
                None => (None, acc),
                Some(node_rc) => {
                    let node_ref = node_rc.borrow();
                    // 1. 递归处理右子树，获得新右子树和右子树的和
                    let (new_right, right_sum) = convert(node_ref.right.clone(), acc);
                    // 2. 当前节点的新值 = 原值 + 右子树的和
                    let new_val = node_ref.val + right_sum;
                    // 3. 递归处理左子树，传递当前新值作为累积和
                    let (new_left, total_sum) = convert(node_ref.left.clone(), new_val);
                    // 4. 创建新节点
                    let new_node = Rc::new(RefCell::new(TreeNode {
                        val: new_val,
                        left: new_left,
                        right: new_right,
                    }));
                    (Some(new_node), total_sum)
                }
            }
        }

        let (new_root, _) = convert(root, 0);
        new_root
    }
}
```
