---
title: "leetcode-二分查找93"
date: 2026-09-28T12:29:16+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 至多一次取反能被 K 整除的最长子数组 II

给你一个整数数组 nums 和一个整数 k。

如果一个子数组的和能够被 k 整除，或者在 将该子数组中的一个元素取反 后能使和被 k 整除，则称该子数组是 有效的 。

将一个元素取反意味着将其值 x 替换为 -x。

返回 最长有效子数组的长度 。如果不存在有效的子数组，则返回 0。

子数组 是数组中一个连续且非空的元素序列。


```
impl Solution {
    pub fn longest_subarray(nums: Vec<i32>, k: i32) -> i32 {
        let k_usize = k as usize;
        // num_pos[c]：2*nums[i] % k == c 的所有下标
        let mut num_pos: Vec<Vec<usize>> = vec![Vec::new(); k_usize];
        // first_pos[c] / last_pos[c]：前缀和 % k == c 的首/末出现位置
        let mut first_pos = vec![-1_i32; k_usize];
        let mut last_pos = vec![0_i32; k_usize];
        first_pos[0] = 0;
        last_pos[0] = 0;

        let mut sum = 0_usize; // 前缀和模 k
        let mut ans = 0_i32;

        for (i, &v) in nums.iter().enumerate() {
            // 用 rem_euclid 保证非负，等价于 C++ 中的 nums[i] % k + k
            let x = (v as i64).rem_euclid(k as i64) as usize;
            num_pos[(2 * x) % k_usize].push(i);

            sum = (sum + x) % k_usize;
            let r = (i + 1) as i32;
            let l = first_pos[sum];
            if l < 0 {
                first_pos[sum] = r;
            } else {
                // 不取反时的最长有效子数组
                ans = ans.max(r - l);
            }
            last_pos[sum] = r;
        }

        // 枚举左右端点的前缀和余数，检查是否存在需要取反的元素
        for sl in 0..k_usize {
            let l = first_pos[sl];
            if l < 0 {
                continue;
            }
            for sr in 0..k_usize {
                let r = last_pos[sr];
                if r - l <= ans {
                    continue;
                }
                // 需要 2*nums[i] % k == (sr - sl) % k
                let pos = &num_pos[(sr + k_usize - sl) % k_usize];
                let idx = pos.partition_point(|&p| (p as i32) < l);
                if idx < pos.len() && (pos[idx] as i32) < r {
                    ans = r - l;
                }
            }
        }

        ans
    }
}
```
