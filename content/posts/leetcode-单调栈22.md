---
title: "leetcode-单调栈22"
date: 2026-06-07T10:14:04+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 叶值的最小代价生成树

给你一个正整数数组 arr，考虑所有满足以下条件的二叉树：

· 每个节点都有 0 个或是 2 个子节点。
· 数组 arr 中的值与树的中序遍历中每个叶节点的值一一对应。
· 每个非叶节点的值等于其左子树和右子树中叶节点的最大值的乘积。

在所有这样的二叉树中，返回每个非叶节点的值的最小可能总和。这个和的值是一个 32 位整数。

如果一个节点有 0 个子节点，那么该节点为叶节点。

```
impl Solution {
    /// 计算叶子节点数组生成的最小代价树（每个非叶子节点值 = 左右子树最大叶子值的乘积）
    ///
    /// # 算法核心
    /// 单调递减栈 + 贪心配对：每次将较小的叶子与相邻的较小值配对，消除较大值带来的成本
    ///
    /// # 时间复杂度 O(n)  空间复杂度 O(n)
    pub fn mct_from_leaf_values(arr: Vec<i32>) -> i32 {
        use std::collections::VecDeque;

        let (mut queue, mut min_sum) = (VecDeque::new(), 0);  // queue: 单调递减栈, min_sum: 累加乘积和

        queue.push_back(i32::MAX);  // 哨兵，避免栈空判断，确保 back() 永远有值

        for a in arr {
            // 当前值 a 破坏了单调递减性，需要处理栈顶所有 <= a 的元素
            while queue.back().unwrap() <= &a {
                let top = queue.pop_back().unwrap();          // 弹出较小的栈顶元素
                // 关键配对：当前 a 与栈内下一个元素（top 的邻居）中的较小值相乘
                // 因为 top 是最小的，选择 min(a, next) 能最小化乘积
                min_sum += top * a.min(*queue.back().unwrap());
            }
            queue.push_back(a);  // 当前元素入栈，保持栈单调递减
        }

        // 栈中还剩 >2 个元素（含哨兵），从左到右依次配对相邻元素
        while queue.len() > 2 {
            min_sum += queue.pop_back().unwrap() * queue.back().unwrap();
        }

        min_sum
    }
}
```
