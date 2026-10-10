---
title: "leetcode-排序7"
date: 2026-10-05T00:34:59+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 最大整除子集

给你一个由 无重复 正整数组成的集合 nums ，请你找出并返回其中最大的整除子集 answer ，子集中每一元素对 (answer[i], answer[j]) 都应当满足：

answer[i] % answer[j] == 0 ，或
answer[j] % answer[i] == 0

如果存在多个有效解子集，返回其中任何一个均可。


```
impl Solution {
    /// 找出最大的整除子集
    ///
    /// 思路：先排序，使整除关系只能从左（小）向右（大）成立，从而可用 DP。
    /// f[i] 表示以 nums[i] 结尾的最长整除子集的长度，
    /// 转移：若 nums[j] | nums[i]，则 f[i] = max(f[i], f[j] + 1)。
    /// 再从最优终点 k 反向回溯，依次挑选满足整除且长度递减的元素，得到具体子集。
    pub fn largest_divisible_subset(mut nums: Vec<i32>) -> Vec<i32> {
        nums.sort_unstable();
        let n = nums.len();

        let mut f = vec![1usize; n]; // f[i]: 以 i 结尾的最长整除子集长度
        let mut best = 0;           // 最长子集的结尾下标

        for i in 0..n {
            for j in 0..i {
                if nums[i] % nums[j] == 0 {
                    f[i] = f[i].max(f[j] + 1);
                }
            }
            if f[i] > f[best] {
                best = i;
            }
        }

        // 从最长子集尾部反向回溯构造答案
        let mut ans = Vec::with_capacity(f[best]);
        let mut k = best;
        let mut len = f[best];
        for i in (0..=k).rev() {
            // 整除成立且长度恰好递增一格，说明 i 是子集中的前驱
            if nums[k] % nums[i] == 0 && f[i] == len {
                ans.push(nums[i]);
                k = i;
                len -= 1;
            }
        }

        ans
    }
}
```
