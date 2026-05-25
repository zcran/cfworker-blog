---
title: "leetcode-记忆化13"
date: 2026-05-06T20:22:33+08:00
tags: ["leetcode", "记忆化"]
draft: false
---



## 所有可能的真二叉树

给你一个整数 n ，请你找出所有可能含 n 个节点的 真二叉树 ，并以列表形式返回。答案中每棵树的每个节点都必须符合 Node.val == 0 。

答案的每个元素都是一棵真二叉树的根节点。你可以按 任意顺序 返回最终的真二叉树列表。

真二叉树 是一类二叉树，树中每个节点恰好有 0 或 2 个子节点。


```
use std::rc::Rc;
use std::cell::RefCell;

// 定义类型别名，简化后续代码
type FullBinaryTree = Option<Rc<RefCell<TreeNode>>>;

impl Solution {
    /// 生成所有可能的由 `n` 个节点构成的满二叉树。
    /// 满二叉树定义：每个节点要么没有子节点，要么有两个子节点。
    /// 注意：节点数 `n` 必须为奇数，否则没有合法树。
    pub fn all_possible_fbt(n: i32) -> Vec<FullBinaryTree> {
        // 满二叉树的节点数只能是奇数
        if n % 2 == 0 {
            return Vec::new();
        }

        // 基础情况：只有一个节点，即叶节点
        if n == 1 {
            return vec![Some(Rc::new(RefCell::new(TreeNode::new(0))))];
        }

        let mut result = Vec::new();

        // 遍历所有可能的左子树节点数（必须是奇数）
        for left_size in (1..n).step_by(2) {
            let right_size = n - 1 - left_size; // 根节点占 1，剩余给左右子树

            // 递归生成左右子树的所有可能形态
            let left_trees = Self::all_possible_fbt(left_size);
            let right_trees = Self::all_possible_fbt(right_size);

            // 组合左右子树：经典的双重循环，使用函数式迭代器提高可读性
            for left in &left_trees {
                for right in &right_trees {
                    // 创建新根节点，左右子树共享（通过 Rc 克隆）
                    let root = Rc::new(RefCell::new(TreeNode {
                        val: 0,
                        left: left.clone(),
                        right: right.clone(),
                    }));
                    result.push(Some(root));
                }
            }
        }

        result
    }
}
```
