---
title: "leetcode-二叉搜索树29"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 二叉搜索树染色


每位勇士面前设有一个二叉搜索树的模型，模型的根节点为 root，树上的各个节点值均不重复。初始时，所有节点均为蓝色。现在按顺序对这棵二叉树进行若干次操作， ops[i] = [type, x, y] 表示第 i 次操作为：

type 等于 0 时，将节点值范围在 [x, y] 的节点均染蓝
type 等于 1 时，将节点值范围在 [x, y] 的节点均染红
请返回完成所有染色后，该二叉树中红色节点的数量。

注意：

题目保证对于每个操作的 x、y 值定出现在二叉搜索树节点中

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn get_number(root: Option<Rc<RefCell<TreeNode>>>, ops: Vec<Vec<i32>>) -> i32 {
        // 中序遍历生成有序数组（纯递归）
        let values = {
            fn inorder(node: &Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
                match node {
                    None => Vec::new(),
                    Some(rc) => {
                        let n = rc.borrow();
                        let mut left = inorder(&n.left);
                        let mut right = inorder(&n.right);
                        left.push(n.val);
                        left.extend(right);
                        left
                    }
                }
            }
            inorder(&root)
        };

        // 逆序处理，累加答案
        ops.into_iter()
            .rev()
            .fold((0, values), |(ans, mut vals), op| {
                let l = vals.partition_point(|&x| x < op[1]);
                let r = vals.partition_point(|&x| x <= op[2]);
                let new_ans = if op[0] == 1 { ans + (r - l) as i32 } else { ans };
                vals.drain(l..r);
                (new_ans, vals)
            })
            .0
    }
}
```
