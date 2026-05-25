---
title: "leetcode-位掩码31"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 找出分数最低的排列

给你一个数组 nums ，它是 [0, 1, 2, ..., n - 1] 的一个排列 。对于任意一个 [0, 1, 2, ..., n - 1] 的排列 perm ，其 分数 定义为：

score(perm) = |perm[0] - nums[perm[1]]| + |perm[1] - nums[perm[2]]| + ... + |perm[n - 1] - nums[perm[0]]|

返回具有 最低 分数的排列 perm 。如果存在多个满足题意且分数相等的排列，则返回其中字典序最小的一个。


```
impl Solution {
    /// 返回一个最小化代价的排列（索引排列，首元素固定为 0）
    pub fn find_permutation(a: Vec<i32>) -> Vec<i32> {
        let n = a.len();
        let full = (1 << n) - 1;
        let size = 1 << n;
        // 扁平化 memo：memo[mask * n + j] = 从状态 (mask, j) 到完成的最小代价
        let mut memo = vec![-1; size * n];

        // 递归函数：返回从 (mask, last) 到结束的最小代价
        fn dfs(mask: usize, last: usize, memo: &mut [i32], a: &[i32], full: usize, n: usize) -> i32 {
            let idx = mask * n + last;
            if memo[idx] != -1 {
                return memo[idx];
            }
            if mask == full {
                let cost = (last as i32 - a[0]).abs();
                memo[idx] = cost;
                return cost;
            }
            let mut best = i32::MAX;
            for nxt in 1..n {
                if (mask >> nxt) & 1 == 0 {
                    let new_mask = mask | (1 << nxt);
                    let sub = dfs(new_mask, nxt, memo, a, full, n);
                    let cur = sub + (last as i32 - a[nxt]).abs();
                    if cur < best {
                        best = cur;
                    }
                }
            }
            memo[idx] = best;
            best
        }

        // 预计算所有状态，填充 memo
        dfs(1, 0, &mut memo, &a, full, n);

        // 构造最优排列
        let mut perm = Vec::with_capacity(n);
        let mut mask = 1;
        let mut last = 0;
        perm.push(0);
        while mask != full {
            let cur_best = memo[mask * n + last];
            for nxt in 1..n {
                if (mask >> nxt) & 1 == 0 {
                    let new_mask = mask | (1 << nxt);
                    let sub = memo[new_mask * n + nxt];
                    let candidate = sub + (last as i32 - a[nxt]).abs();
                    if candidate == cur_best {
                        perm.push(nxt);
                        mask = new_mask;
                        last = nxt;
                        break;
                    }
                }
            }
        }
        perm.into_iter().map(|x| x as i32).collect()
    }
}
```
