---
title: "leetcode-二叉树66"
date: 2026-08-15T11:32:31+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 从二叉树一个节点到另一个节点每一步的方向

给你一棵 二叉树 的根节点 root ，这棵二叉树总共有 n 个节点。每个节点的值为 1 到 n 中的一个整数，且互不相同。给你一个整数 startValue ，表示起点节点 s 的值，和另一个不同的整数 destValue ，表示终点节点 t 的值。

请找到从节点 s 到节点 t 的 最短路径 ，并以字符串的形式返回每一步的方向。每一步用 大写 字母 'L' ，'R' 和 'U' 分别表示一种方向：

'L' 表示从一个节点前往它的 左孩子 节点。
'R' 表示从一个节点前往它的 右孩子 节点。
'U' 表示从一个节点前往它的 父 节点。

请你返回从 s 到 t 最短路径 每一步的方向。


```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    pub fn get_directions(root: Option<Rc<RefCell<TreeNode>>>, start_value: i32, dest_value: i32) -> String {
        // 先找到从根到 s 和 t 的路径字符串
        let mut s_path = String::new();
        let mut t_path = String::new();
        Self::find(root.clone(), start_value, &mut s_path);
        Self::find(root, dest_value, &mut t_path);

        // 去掉公共前缀（LCA 以上的部分）
        let common = s_path.bytes().zip(t_path.bytes()).take_while(|(a, b)| a == b).count();
        let up = "U".repeat(s_path.len() - common);
        let down = &t_path[common..];
        up + down
    }

    /// 从 root 出发 DFS 查找 target，将路径方向记录到 path
    /// 找到返回 true，否则返回 false 并清空 path
    fn find(node: Option<Rc<RefCell<TreeNode>>>, target: i32, path: &mut String) -> bool {
        let Some(n) = node else { return false };
        let n = n.borrow();

        if n.val == target {
            return true;
        }

        path.push('L');
        if Self::find(n.left.clone(), target, path) {
            return true;
        }
        path.pop();

        path.push('R');
        if Self::find(n.right.clone(), target, path) {
            return true;
        }
        path.pop();

        false
    }
}
```
