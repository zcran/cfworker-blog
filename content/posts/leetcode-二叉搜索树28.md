---
title: "leetcode-二叉搜索树28"
date: 2026-05-14T20:16:15+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 二叉搜索树最近节点查询


给你一个 二叉搜索树 的根节点 root ，和一个由正整数组成、长度为 n 的数组 queries 。

请你找出一个长度为 n 的 二维 答案数组 answer ，其中 answer[i] = [mini, maxi] ：

mini 是树中小于等于 queries[i] 的 最大值 。如果不存在这样的值，则使用 -1 代替。
maxi 是树中大于等于 queries[i] 的 最小值 。如果不存在这样的值，则使用 -1 代替。

返回数组 answer 。

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 对于每个查询 q，返回 [小于等于 q 的最大值, 大于等于 q 的最小值]。
    /// 若不存在则对应值为 -1。
    ///
    /// 算法：
    /// 1. 中序遍历 BST 得到升序数组 `vals`。
    /// 2. 对每个查询 q，二分查找第一个 ≥ q 的位置，然后计算前后邻居。
    pub fn closest_nodes(root: Option<Rc<RefCell<TreeNode>>>, queries: Vec<i32>) -> Vec<Vec<i32>> {
        // 中序遍历收集所有节点值（BST 中序即为升序）
        let mut vals = Vec::new();
        Self::inorder_collect(&root, &mut vals);

        // 对每个查询，二分查找并构造结果
        queries
            .iter()
            .map(|&q| {
                let idx = vals.partition_point(|&x| x < q); // 第一个 ≥ q 的位置
                let mx = vals.get(idx).copied().unwrap_or(-1); // 大于等于 q 的最小值
                let mn = if idx < vals.len() && vals[idx] == q {
                    q
                } else {
                    vals.get(idx.wrapping_sub(1)).copied().unwrap_or(-1) // 小于 q 的最大值
                };
                vec![mn, mx]
            })
            .collect()
    }

    /// 中序遍历，将节点值按序推入 `out`
    fn inorder_collect(node: &Option<Rc<RefCell<TreeNode>>>, out: &mut Vec<i32>) {
        if let Some(rc) = node {
            let n = rc.borrow();
            Self::inorder_collect(&n.left, out);
            out.push(n.val);
            Self::inorder_collect(&n.right, out);
        }
    }
}
```


第二种
```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn closest_nodes(root: Option<Rc<RefCell<TreeNode>>>, queries: Vec<i32>) -> Vec<Vec<i32>> {
        // 中序遍历得到升序数组
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

        let vals = inorder(&root);

        queries
            .into_iter()
            .map(|q| {
                let idx = vals.partition_point(|&x| x < q); // 第一个 >= q 的位置
                let mx = vals.get(idx).copied().unwrap_or(-1); // 大于等于 q 的最小值
                let mn = if idx < vals.len() && vals[idx] == q {
                    q // q 存在，则小于等于 q 的最大值就是 q 本身
                } else {
                    vals.get(idx.wrapping_sub(1)).copied().unwrap_or(-1) // 前一个元素（小于 q 的最大值）
                };
                vec![mn, mx]
            })
            .collect()
    }
}
```
