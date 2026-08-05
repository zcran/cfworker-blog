---
title: "leetcode-枚举81"
date: 2026-07-09T10:05:03+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 不同 XOR 三元组的数目 II

给你一个整数数组 nums 。

XOR 三元组 定义为三个元素的异或值 nums[i] XOR nums[j] XOR nums[k]，其中 i <= j <= k。

返回所有可能三元组 (i, j, k) 中 不同 的 XOR 值的数量。


```
impl Solution {
    pub fn unique_xor_triplets(nums: Vec<i32>) -> i32 {
        let n = nums.len();

        // 找到数组中最大值，确定需要的位数
        let max_val = *nums.iter().max().unwrap();
        // 位宽：能表示 max_val 所需的最小位数
        let bit_width = (max_val as f64).log2().floor() as usize + 1;
        let u = 1 << bit_width; // 值域大小

        // has_xy[x] 表示是否存在 i <= j 使得 nums[i] ^ nums[j] = x
        let mut has_xy = vec![false; u];
        for i in 0..n {
            for j in i..n {
                let xor_val = (nums[i] ^ nums[j]) as usize;
                has_xy[xor_val] = true;
            }
        }

        // has_xyz[x] 表示是否存在 i <= j <= k 使得 nums[i] ^ nums[j] ^ nums[k] = x
        let mut has_xyz = vec![false; u];
        for xy in 0..u {
            if has_xy[xy] {
                for &z in &nums {
                    let xor_val = (xy as i32 ^ z) as usize;
                    has_xyz[xor_val] = true;
                }
            }
        }

        // 统计 true 的数量
        has_xyz.iter().filter(|&&v| v).count() as i32
    }
}
```
