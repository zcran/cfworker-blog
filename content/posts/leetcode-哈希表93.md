---
title: "leetcode-哈希表93"
date: 2026-10-09T20:08:01+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 至多一次取反能被 K 整除的最长子数组 I

给你一个整数数组 nums 和一个整数 k。

如果一个子数组的和能够被 k 整除，或者在 将该子数组中的一个元素取反 后能使和被 k 整除，则称该子数组是 有效的 。

将一个元素取反意味着将其值 x 替换为 -x。

返回 最长有效子数组的长度 。如果不存在有效的子数组，则返回 0。

子数组 是数组中一个连续且非空的元素序列。


```
impl Solution {
    pub fn longest_subarray(nums: Vec<i32>, k: i32) -> i32 {
        let n = nums.len();
        let k64 = k as i64;

        // 前缀和模 k，pre[i] = (nums[0..i] 之和) mod k
        let mut pre = vec![0i64; n + 1];
        // fan[i] = (2 * nums[i]) mod k
        // 取反 nums[i] 会使区间和变化 -2*nums[i]，故只需关心其模 k 的余数
        let mut fan = vec![0usize; n];
        for i in 0..n {
            pre[i + 1] = (pre[i] + nums[i] as i64).rem_euclid(k64);
            fan[i] = (2 * nums[i] as i64).rem_euclid(k64) as usize;
        }

        // 从最长的长度开始尝试，找到第一个可行的即可返回
        for len in (1..=n).rev() {
            // cnt[r] 记录当前窗口中 fan 值模 k 等于 r 的元素个数
            let mut cnt = vec![0i32; k as usize];
            for &f in &fan[..len] {
                cnt[f] += 1;
            }

            // 检查初始窗口 [0, len)
            let mut s = ((pre[len] - pre[0]).rem_euclid(k64)) as usize;
            if s == 0 || cnt[s] > 0 {
                return len as i32;
            }

            // 滑动窗口检查其余位置
            for left in 1..=(n - len) {
                cnt[fan[left - 1]] -= 1;
                cnt[fan[left + len - 1]] += 1;

                s = ((pre[left + len] - pre[left]).rem_euclid(k64)) as usize;
                if s == 0 || cnt[s] > 0 {
                    return len as i32;
                }
            }
        }

        0
    }
}
```
