---
title: "leetcode-回溯37"
date: 2026-07-04T10:22:03+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 从叶结点开始的最小字符串


给定一颗根结点为 root 的二叉树，树中的每一个结点都有一个 [0, 25] 范围内的值，分别代表字母 'a' 到 'z'。

返回 按字典序最小 的字符串，该字符串从这棵树的一个叶结点开始，到根结点结束。

注：字符串中任何较短的前缀在 字典序上 都是 较小 的：

例如，在字典序上 "ab" 比 "aba" 要小。叶结点是指没有子结点的结点。

节点的叶节点是没有子节点的节点。

```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 返回从叶子到根的最小字典序字符串
    ///
    /// 思路：DFS 遍历所有叶子节点，构建从根到叶子的路径，反转后比较字典序
    pub fn smallest_from_leaf(root: Option<Rc<RefCell<TreeNode>>>) -> String {
        let mut result = None;
        let mut path = Vec::new();

        fn dfs(
            node: Option<Rc<RefCell<TreeNode>>>,
            path: &mut Vec<u8>,
            result: &mut Option<String>,
        ) {
            let node = match node {
                Some(n) => n,
                None => return,
            };

            // 添加当前节点字符
            let val = node.borrow().val as u8;
            path.push(val + b'a');

            let left = node.borrow().left.clone();
            let right = node.borrow().right.clone();

            // 叶子节点：构建字符串并比较
            if left.is_none() && right.is_none() {
                // 从叶子到根，需要反转路径
                let s: String = path.iter().rev().map(|&c| c as char).collect();

                if let Some(best) = result {
                    if s < *best {
                        *best = s;
                    }
                } else {
                    *result = Some(s);
                }
            } else {
                // 递归遍历子节点
                dfs(left, path, result);
                dfs(right, path, result);
            }

            // 回溯
            path.pop();
        }

        dfs(root, &mut path, &mut result);
        result.unwrap_or_default()
    }
}
```
