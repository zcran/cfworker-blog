---
title: "leetcode-数论35"
date: 2026-06-20T11:09:56+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 最大公约数遍历

给你一个下标从 0 开始的整数数组 nums ，你可以在一些下标之间遍历。对于两个下标 i 和 j（i != j），当且仅当 gcd(nums[i], nums[j]) > 1 时，我们可以在两个下标之间通行，其中 gcd 是两个数的 最大公约数 。

你需要判断 nums 数组中 任意 两个满足 i < j 的下标 i 和 j ，是否存在若干次通行可以从 i 遍历到 j 。

如果任意满足条件的下标对都可以遍历，那么返回 true ，否则返回 false 。



```
use std::collections::HashMap;

impl Solution {
    /// 判断是否所有下标对都可以通过质因子连通
    ///
    /// 核心思路：建立质因子并查集
    /// 1. 每个数与其所有质因子连通
    /// 2. 如果两个数共享质因子，它们就在同一连通分量
    /// 3. 最终检查所有数是否在同一分量
    pub fn can_traverse_all_pairs(nums: Vec<i32>) -> bool {
        let n = nums.len();
        if n == 1 {
            return true;
        }

        // 处理包含 1 的情况：1 没有质因子，无法与其他数连通
        if nums.iter().any(|&x| x == 1) {
            return false;
        }

        // 预处理所有数的质因子（缓存）
        let factors = precompute_factors(&nums);

        // 使用 HashMap 作为并查集（稀疏存储）
        let mut parent: HashMap<i32, i32> = HashMap::new();

        // 并查集查找（带路径压缩）
        fn find(parent: &mut HashMap<i32, i32>, x: i32) -> i32 {
            // 如果 x 不存在，初始化为自身
            if !parent.contains_key(&x) {
                parent.insert(x, x);
                return x;
            }

            let mut p = x;
            // 找到根节点
            while parent[&p] != p {
                p = parent[&p];
            }
            // 路径压缩
            let root = p;
            let mut cur = x;
            while parent[&cur] != cur {
                let next = parent[&cur];
                parent.insert(cur, root);
                cur = next;
            }
            root
        }

        // 并查集合并
        fn union(parent: &mut HashMap<i32, i32>, a: i32, b: i32) {
            let ra = find(parent, a);
            let rb = find(parent, b);
            if ra != rb {
                parent.insert(rb, ra);
            }
        }

        // 将每个数与其所有质因子连通
        for (i, &num) in nums.iter().enumerate() {
            // 使用 i 作为唯一标识，避免与质因子冲突
            let node_id = -(i as i32 + 1);
            union(&mut parent, node_id, num);

            if let Some(factor_list) = factors.get(&num) {
                for &prime in factor_list {
                    union(&mut parent, node_id, prime);
                }
            }
        }

        // 检查所有数是否在同一连通分量
        let root = find(&mut parent, -(0_i32 + 1));
        for i in 1..n {
            let node_id = -(i as i32 + 1);
            if find(&mut parent, node_id) != root {
                return false;
            }
        }

        true
    }
}

/// 预计算多个数的质因子（带缓存）
fn precompute_factors(nums: &[i32]) -> HashMap<i32, Vec<i32>> {
    let mut cache = HashMap::new();

    for &num in nums {
        if cache.contains_key(&num) {
            continue;
        }

        let mut x = num;
        let mut factors = Vec::new();
        let mut d = 2;

        // 分解质因子
        while d * d <= x {
            if x % d == 0 {
                factors.push(d);
                while x % d == 0 {
                    x /= d;
                }
            }
            d += 1;
        }
        if x > 1 {
            factors.push(x);
        }

        cache.insert(num, factors);
    }

    cache
}
```
