---
title: "leetcode-排序15"
date: 2026-10-05T00:35:00+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 带因子的二叉树

给出一个含有不重复整数元素的数组 arr ，每个整数 arr[i] 均大于 1。

用这些整数来构建二叉树，每个整数可以使用任意次数。其中：每个非叶结点的值应等于它的两个子结点的值的乘积。

满足条件的二叉树一共有多少个？答案可能很大，返回 对 10^9 + 7 取余 的结果。


```
use std::collections::HashMap;

const MOD: i64 = 1_000_000_007;

impl Solution {
    /// 统计能构成「每个非叶结点值 = 两子结点值之积」的二叉树数量
    ///
    /// 思路：排序后从小到大 DP。设 dp[x] 为以 x 为根、由 arr 中元素构成的合法二叉树数量。
    /// 对当前根 n，枚举因子 k（k 为 arr 中元素且 n/k 也在 arr 中）作为左子树根，
    /// 左、右子树独立，贡献 dp[k] * dp[n/k]；再加上「n 本身作为叶子」的 1 种。
    /// 最终答案为所有 dp[x] 之和（每个结点都可作为整棵树的根）。
    /// 时间 O(n²)（枚举因子对），额外空间 O(n)。
    pub fn num_factored_binary_trees(mut arr: Vec<i32>) -> i32 {
        arr.sort_unstable();
        let mut dp: HashMap<i32, i64> = HashMap::with_capacity(arr.len());
        let mut total: i64 = 0;

        for &n in &arr {
            // 至少可以是叶子结点
            let mut ways: i64 = 1;

            // 枚举左子树根 k：需满足 k 为 n 的因子且 n/k 也在 arr 中
            for (&k, &v) in &dp {
                if n % k == 0 {
                    if let Some(&w) = dp.get(&(n / k)) {
                        ways = (ways + v * w) % MOD;
                    }
                }
            }

            dp.insert(n, ways);
            total = (total + ways) % MOD;
        }

        total as i32
    }
}
```
