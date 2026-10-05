---
title: "leetcode-树26"
date: 2026-09-24T09:47:34+08:00
tags: ["leetcode", "树"]
draft: false
---


## 二叉树染色

小扣有一个根结点为 root 的二叉树模型，初始所有结点均为白色，可以用蓝色染料给模型结点染色，模型的每个结点有一个 val 价值。小扣出于美观考虑，希望最后二叉树上每个蓝色相连部分的结点个数不能超过 k 个，求所有染成蓝色的结点价值总和最大是多少？




```
use std::rc::Rc;
use std::cell::RefCell;

impl Solution {
    /// 树形 DP
    ///
    /// 状态定义：
    ///   dp[u][i]：在以 u 为根的子树中，且包含 u 的蓝色连通块大小为 i 时，
    ///             子树内蓝色节点价值总和的最大值。
    ///   注意：这里 i = 0 表示 u 不染色，此时左右子树可以各自独立取最优。
    ///
    /// 转移：
    ///   设左子树 dp_l，右子树 dp_r。
    ///   - u 不染色：dp[u][0] = max(dp_l) + max(dp_r)
    ///   - u 染色，连通块大小为 i（1 ≤ i ≤ k）：
    ///       枚举左子树中与 u 相连的连通块大小 j（0 ≤ j < i），
    ///       右子树中与 u 相连的连通块大小则为 i - 1 - j。
    ///       dp[u][i] = max( val[u] + dp_l[j] + dp_r[i-1-j] )
    ///
    /// 最终答案 = max(dp[root])。
    pub fn max_value(root: Option<Rc<RefCell<TreeNode>>>, k: i32) -> i32 {
        let k = k as usize;

        // 迭代后序遍历，避免递归深度过大
        // 由于 TreeNode 是 Rc<RefCell>，先收集所有节点及后序顺序
        let root = match root {
            Some(r) => r,
            None => return 0,
        };

        // 后序顺序：先左、再右、最后根
        let mut order = Vec::new();
        let mut stack = vec![root.clone()];
        let mut visited = std::collections::HashSet::new();

        while let Some(node) = stack.pop() {
            let ptr = Rc::as_ptr(&node) as usize;
            if visited.contains(&ptr) {
                order.push(node);
            } else {
                visited.insert(ptr);
                stack.push(node.clone());
                if let Some(right) = node.borrow().right.clone() {
                    stack.push(right);
                }
                if let Some(left) = node.borrow().left.clone() {
                    stack.push(left);
                }
            }
        }

        // dp 存储每个节点的 DP 数组
        let mut dp_map: std::collections::HashMap<usize, Vec<i32>> = std::collections::HashMap::new();

        for node in order {
            let ptr = Rc::as_ptr(&node) as usize;
            let val = node.borrow().val;

            // 获取左右子树的 dp（若为空则全 0）
            let left_dp = node
                .borrow()
                .left
                .as_ref()
                .map(|l| dp_map[&(Rc::as_ptr(l) as usize)].clone())
                .unwrap_or_else(|| vec![0; k + 1]);
            let right_dp = node
                .borrow()
                .right
                .as_ref()
                .map(|r| dp_map[&(Rc::as_ptr(r) as usize)].clone())
                .unwrap_or_else(|| vec![0; k + 1]);

            let mut dp = vec![0i32; k + 1];

            // u 不染色：左右子树各自取最优
            let left_max = *left_dp.iter().max().unwrap();
            let right_max = *right_dp.iter().max().unwrap();
            dp[0] = left_max + right_max;

            // u 染色：连通块大小为 i
            for i in 1..=k {
                for j in 0..i {
                    let r_size = i - 1 - j;
                    if r_size <= k {
                        let candidate = val + left_dp[j] + right_dp[r_size];
                        if candidate > dp[i] {
                            dp[i] = candidate;
                        }
                    }
                }
            }

            dp_map.insert(ptr, dp);
        }

        let root_ptr = Rc::as_ptr(&root) as usize;
        *dp_map[&root_ptr].iter().max().unwrap()
    }
}
```
