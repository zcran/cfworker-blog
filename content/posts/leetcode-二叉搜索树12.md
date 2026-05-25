---
title: "leetcode-二叉搜索树12"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 二叉搜索树中的众数

给你一个含重复值的二叉搜索树（BST）的根节点 root ，找出并返回 BST 中的所有 众数（即，出现频率最高的元素）。

如果树中有不止一个众数，可以按 任意顺序 返回。

假定 BST 满足如下定义：

结点左子树中所含节点的值 小于等于 当前节点的值
结点右子树中所含节点的值 大于等于 当前节点的值
左子树和右子树都是二叉搜索树

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn find_mode(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
        // 生成中序遍历序列（升序）
        let mut seq = Vec::new();
        fn inorder(node: Option<Rc<RefCell<TreeNode>>>, seq: &mut Vec<i32>) {
            if let Some(n) = node {
                let n_ref = n.borrow();
                inorder(n_ref.left.clone(), seq);
                seq.push(n_ref.val);
                inorder(n_ref.right.clone(), seq);
            }
        }
        inorder(root, &mut seq);

        // 函数式方式：使用 fold 统计众数
        if seq.is_empty() {
            return Vec::new();
        }

        let (result, _, _, _) = seq.iter().fold(
            (Vec::new(), seq[0], 0usize, 0usize),
            |(mut res, cur_val, cur_cnt, max_cnt), &val| {
                let (new_cur_val, new_cur_cnt) = if val == cur_val {
                    (cur_val, cur_cnt + 1)
                } else {
                    (val, 1)
                };
                let new_max_cnt = max_cnt.max(new_cur_cnt);
                let new_res = if new_cur_cnt == new_max_cnt {
                    if new_max_cnt > max_cnt {
                        vec![new_cur_val]
                    } else {
                        res.push(new_cur_val);
                        res
                    }
                } else {
                    res
                };
                (new_res, new_cur_val, new_cur_cnt, new_max_cnt)
            },
        );
        result
    }
}
```
