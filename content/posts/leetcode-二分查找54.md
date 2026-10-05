---
title: "leetcode-二分查找54"
date: 2026-09-28T12:29:13+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 使数组 K 递增的最少操作次数

给你一个下标从 0 开始包含 n 个正整数的数组 arr ，和一个正整数 k 。

如果对于每个满足 k <= i <= n-1 的下标 i ，都有 arr[i-k] <= arr[i] ，那么我们称 arr 是 K 递增 的。

· 比方说，arr = [4, 1, 5, 2, 6, 2] 对于 k = 2 是 K 递增的，因为：
    · arr[0] <= arr[2] (4 <= 5)
    · arr[1] <= arr[3] (1 <= 2)
    · arr[2] <= arr[4] (5 <= 6)
    · arr[3] <= arr[5] (2 <= 2)

· 但是，相同的数组 arr 对于 k = 1 不是 K 递增的（因为 arr[0] > arr[1]），对于 k = 3 也不是 K 递增的（因为 arr[0] > arr[3] ）。

每一次 操作 中，你可以选择一个下标 i 并将 arr[i] 改成任意 正整数。

请你返回对于给定的 k ，使数组变成 K 递增的 最少操作次数 。


```
impl Solution {
    pub fn k_increasing(arr: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;
        let mut removals = 0;

        // 每个下标模 k 的余数对应一条独立的链，链内需满足非递减
        // 对每条链，最少修改次数 = 链长 - 最长非递减子序列长度
        for i in 0..k {
            let mut tails: Vec<i32> = Vec::new();
            for j in (i..arr.len()).step_by(k) {
                let x = arr[j];
                // 找第一个 > x 的位置，替换之，维持 tails 单调不减
                let pos = tails.partition_point(|&v| v <= x);
                if pos == tails.len() {
                    tails.push(x);
                } else {
                    tails[pos] = x;
                }
            }
            removals += (arr.len() - i).div_ceil(k) as i32 - tails.len() as i32;
        }

        removals
    }
}
```
