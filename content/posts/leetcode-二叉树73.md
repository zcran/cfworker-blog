---
title: "leetcode-二叉树73"
date: 2026-08-15T11:32:32+08:00
tags: ["leetcode", "二叉树"]
draft: false
---


## 移除子树后的二叉树高度

给你一棵 二叉树 的根节点 root ，树中有 n 个节点。每个节点都可以被分配一个从 1 到 n 且互不相同的值。另给你一个长度为 m 的数组 queries 。

你必须在树上执行 m 个 独立 的查询，其中第 i 个查询你需要执行以下操作：

· 从树中 移除 以 queries[i] 的值作为根节点的子树。题目所用测试用例保证 queries[i] 不 等于根节点的值。

返回一个长度为 m 的数组 answer ，其中 answer[i] 是执行第 i 个查询后树的高度。

注意：

查询之间是独立的，所以在每个查询执行后，树会回到其 初始 状态。
树的高度是从根到树中某个节点的 最长简单路径中的边数 。


```
use std::cell::RefCell;
use std::collections::HashMap;
use std::rc::Rc;

impl Solution {
    pub fn tree_queries(root: Option<Rc<RefCell<TreeNode>>>, queries: Vec<i32>) -> Vec<i32> {
        let mut depth = HashMap::new();
        let mut height = HashMap::new();
        let mut max1 = HashMap::new();   // 每层最大高度
        let mut max2 = HashMap::new();   // 每层次大高度
        let mut cnt_max = HashMap::new(); // 每层最大高度的节点个数

        Self::dfs(&root, 0, &mut depth, &mut height, &mut max1, &mut max2, &mut cnt_max);

        // 预处理答案：删除某节点后，该层剩余最大高度 = 若该节点是唯一最大则取次大，否则不变
        let mut ans_map = HashMap::new();
        for (&val, &dep) in &depth {
            let h = height[&val];
            let m1 = max1[&dep];
            let m2 = max2[&dep];
            let new_h = if h == m1 && cnt_max[&dep] == 1 { m2 } else { m1 };
            ans_map.insert(val, dep as i32 + new_h);
        }

        queries.into_iter().map(|q| ans_map[&q]).collect()
    }

    fn dfs(
        node: &Option<Rc<RefCell<TreeNode>>>,
        d: usize,
        depth: &mut HashMap<i32, usize>,
        height: &mut HashMap<i32, i32>,
        max1: &mut HashMap<usize, i32>,
        max2: &mut HashMap<usize, i32>,
        cnt_max: &mut HashMap<usize, usize>,
    ) -> i32 {
        let Some(n) = node else { return -1 };
        let n = n.borrow();
        let v = n.val;

        depth.insert(v, d);

        let lh = Self::dfs(&n.left, d + 1, depth, height, max1, max2, cnt_max);
        let rh = Self::dfs(&n.right, d + 1, depth, height, max1, max2, cnt_max);
        let h = 1 + lh.max(rh);
        height.insert(v, h);

        // 更新该层最大/次大高度及最大高度的节点个数
        let cur_m1 = max1.get(&d).copied().unwrap_or(-1);
        if h > cur_m1 {
            max2.insert(d, cur_m1);
            max1.insert(d, h);
            cnt_max.insert(d, 1);
        } else if h == cur_m1 {
            *cnt_max.entry(d).or_insert(0) += 1;
        } else if h > max2.get(&d).copied().unwrap_or(-1) {
            max2.insert(d, h);
        }

        h
    }
}
```
