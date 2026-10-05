---
title: "leetcode-贪心97"
date: 2026-10-03T12:55:40+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 向数组中追加 K 个整数

给你一个整数数组 nums 和一个整数 k 。请你向 nums 中追加 k 个 未 出现在 nums 中的、互不相同 的 正 整数，并使结果数组的元素和 最小 。

返回追加到 nums 中的 k 个整数之和。


```
impl Solution {
    pub fn minimal_k_sum(mut nums: Vec<i32>, k: i32) -> i64 {
        // 排序并去重，便于按顺序找到缺失的正整数
        nums.sort_unstable();
        nums.dedup();

        let mut k = k as i64;       // 还需补充的整数个数
        let mut ans = 0i64;         // 补充的整数之和
        let mut next = 1i64;        // 下一个待补充的正整数

        for &x in &nums {
            let x = x as i64;
            // [next, x) 区间内缺失的正整数个数
            let gap = x - next;

            if gap >= k {
                // 缺口足够，只需补最小的 k 个连续整数：next, next+1, ..., next+k-1
                ans += (next + next + k - 1) * k / 2;
                return ans;
            }

            // 缺口不够，全部补上
            ans += (next + x - 1) * gap / 2;
            k -= gap;
            next = x + 1; // 跳过已存在的 x
        }

        // 遍历完所有已有数字后，若仍需补充，继续从 next 开始补 k 个连续整数
        if k > 0 {
            ans += (next + next + k - 1) * k / 2;
        }
        ans
    }
}
```
