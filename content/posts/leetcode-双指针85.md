---
title: "leetcode-双指针85"
date: 2026-09-05T09:53:06+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 找出数组中的所有 K 近邻下标

给你一个下标从 0 开始的整数数组 nums 和两个整数 key 和 k 。K 近邻下标 是 nums 中的一个下标 i ，并满足至少存在一个下标 j 使得 |i - j| <= k 且 nums[j] == key 。

以列表形式返回按 递增顺序 排序的所有 K 近邻下标。


```
impl Solution {
    pub fn find_k_distant_indices(nums: Vec<i32>, key: i32, k: i32) -> Vec<i32> {
        let n = nums.len();
        let k = k as usize;
        // 差分数组：标记每个下标是否被某个 key 的 k 邻域覆盖
        let mut diff = vec![0i32; n + 1];

        for (i, &num) in nums.iter().enumerate() {
            if num == key {
                // 当前 key 影响的下标区间 [left, right]
                let left = i.saturating_sub(k);
                let right = (i + k).min(n - 1);
                diff[left] += 1;
                diff[right + 1] -= 1;
            }
        }

        let mut result = Vec::new();
        let mut cover = 0;
        for i in 0..n {
            cover += diff[i];
            if cover > 0 {
                result.push(i as i32);
            }
        }

        result
    }
}
```
