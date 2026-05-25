---
title: "leetcode-位掩码35"
date: 2026-05-18T10:28:00+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 判断连接可整除性


给你一个正整数数组 nums 和一个正整数 k。

当 nums 的一个 排列 中的所有数字，按照排列顺序 连接其十进制表示 后形成的数可以 被 k  整除时，我们称该排列形成了一个 可整除连接 。

返回能够形成 可整除连接 且 字典序 最小 的排列（按整数列表的形式表示）。如果不存在这样的排列，返回一个空列表。


```
use std::collections::VecDeque;

impl Solution {
    /// 返回一个数字的排列，使得将它们按顺序拼接后形成的整数能被 k 整除。
    /// 若不存在这样的排列，返回空向量。
    pub fn concatenated_divisibility(mut nums: Vec<i32>, k: i32) -> Vec<i32> {
        // 排序并非必要，但可让输出稳定，且在某些剪枝中有益
        nums.sort_unstable();

        let n = nums.len();
        let full_mask = (1 << n) - 1;
        let k = k as usize;

        // 预计算每个数的 10^len % k 和 num % k
        let (mod10, val_mod): (Vec<usize>, Vec<usize>) = nums
            .iter()
            .map(|&num| {
                let len = num.to_string().len() as u32;
                let pow10 = 10_usize.pow(len) % k;      // 10^len % k
                let val = (num as usize) % k;            // num % k
                (pow10, val)
            })
            .unzip();

        // BFS 状态记录：parent_rest[rest][mask] 存储前一个余数，last_idx[rest][mask] 存储最后添加的数字下标
        let state_size = 1 << n;
        let mut parent_rest = vec![vec![usize::MAX; state_size]; k];
        let mut last_idx = vec![vec![usize::MAX; state_size]; k];

        let mut queue = VecDeque::new();
        // 初始状态 (余数 0，空集 mask 0)
        parent_rest[0][0] = 0;      // 标记为已访问
        last_idx[0][0] = n;         // 特殊值，表示起点
        queue.push_back((0, 0));    // (rest, mask)

        let mut found = false;
        while let Some((rest, mask)) = queue.pop_front() {
            if mask == full_mask && rest == 0 {
                found = true;
                break;
            }
            // 尝试添加每一个未使用的数字
            for i in 0..n {
                if (mask & (1 << i)) == 0 {
                    let new_rest = (rest * mod10[i] + val_mod[i]) % k;
                    let new_mask = mask | (1 << i);
                    if parent_rest[new_rest][new_mask] == usize::MAX {
                        parent_rest[new_rest][new_mask] = rest;
                        last_idx[new_rest][new_mask] = i;
                        queue.push_back((new_rest, new_mask));
                    }
                }
            }
        }

        if !found {
            return Vec::new();
        }

        // 回溯构建答案
        let mut ans = Vec::with_capacity(n);
        let mut rest = 0;
        let mut mask = full_mask;
        while mask != 0 {
            let i = last_idx[rest][mask];
            ans.push(nums[i]);
            let prev_rest = parent_rest[rest][mask];
            mask ^= 1 << i;
            rest = prev_rest;
        }
        ans.reverse();
        ans
    }
}
```
