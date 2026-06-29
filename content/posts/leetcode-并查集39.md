---
title: "leetcode-并查集39"
date: 2026-06-25T11:22:23+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 每棵子树内缺失的最小基因值

有一棵根节点为 0 的 家族树 ，总共包含 n 个节点，节点编号为 0 到 n - 1 。给你一个下标从 0 开始的整数数组 parents ，其中 parents[i] 是节点 i 的父节点。由于节点 0 是 根 ，所以 parents[0] == -1 。

总共有 105 个基因值，每个基因值都用 闭区间 [1, 105] 中的一个整数表示。给你一个下标从 0 开始的整数数组 nums ，其中 nums[i] 是节点 i 的基因值，且基因值 互不相同 。

请你返回一个数组 ans ，长度为 n ，其中 ans[i] 是以节点 i 为根的子树内 缺失 的 最小 基因值。

节点 x 为根的 子树 包含节点 x 和它所有的 后代 节点。


```
impl Solution {
    pub fn smallest_missing_value_subtree(parents: Vec<i32>, nums: Vec<i32>) -> Vec<i32> {
        let n = parents.len();
        let mut ans = vec![1; n];

        // 找到基因值为 1 的节点（题目保证互不相同）
        let start = match nums.iter().position(|&x| x == 1) {
            Some(i) => i,
            None => return ans,  // 没有 1，所有子树缺失的最小基因值都是 1
        };

        // 构建孩子列表（邻接表）
        let mut children = vec![Vec::new(); n];
        for i in 1..n {
            children[parents[i] as usize].push(i);
        }

        // DFS 遍历子树，收集所有基因值
        // 基因值范围 [1, 100000]，所以数组大小设为 100002 防止越界
        const MAX_GENE: usize = 100_002;
        let mut seen = vec![false; MAX_GENE];

        fn dfs(x: usize, children: &[Vec<usize>], seen: &mut [bool], nums: &[i32]) {
            seen[nums[x] as usize] = true;
            for &child in &children[x] {
                if !seen[nums[child] as usize] {
                    dfs(child, children, seen, nums);
                }
            }
        }

        let mut mex = 1;
        let mut node = start;

        // 从基因值为 1 的节点向上遍历到根
        while node != usize::MAX {
            dfs(node, &children, &mut seen, &nums);

            // 更新 mex：找到第一个未出现的基因值
            while mex < MAX_GENE && seen[mex] {
                mex += 1;
            }

            ans[node] = mex as i32;

            // 向上移动到父节点
            if node == 0 {
                break;
            }
            node = parents[node] as usize;
        }

        ans
    }
}
```
