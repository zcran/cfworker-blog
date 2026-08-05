---
title: "leetcode-滑动窗口85"
date: 2026-07-18T11:02:34+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 拾起 K 个 1 需要的最少行动次数

给你一个下标从 0 开始的二进制数组 nums，其长度为 n ；另给你一个 正整数 k 以及一个 非负整数 maxChanges 。

Alice 在玩一个游戏，游戏的目标是让 Alice 使用 最少 数量的 行动 次数从 nums 中拾起 k 个 1 。游戏开始时，Alice 可以选择数组 [0, n - 1] 范围内的任何索引 aliceIndex 站立。如果 nums[aliceIndex] == 1 ，Alice 会拾起一个 1 ，并且 nums[aliceIndex] 变成0（这 不算 作一次行动）。之后，Alice 可以执行 任意数量 的 行动（包括零次），在每次行动中 Alice 必须 恰好 执行以下动作之一：

选择任意一个下标 j != aliceIndex 且满足 nums[j] == 0 ，然后将 nums[j] 设置为 1 。这个动作最多可以执行 maxChanges 次。

选择任意两个相邻的下标 x 和 y（|x - y| == 1）且满足 nums[x] == 1, nums[y] == 0 ，然后交换它们的值（将 nums[y] = 1 和 nums[x] = 0）。如果 y == aliceIndex，在这次行动后 Alice 拾起一个 1 ，并且 nums[y] 变成 0 。

返回 Alice 拾起 恰好 k 个 1 所需的 最少 行动次数。


```
impl Solution {
    /// 计算索引 i 及其左右相邻位置（i-1, i, i+1）中 1 的数量
    fn count_nearby_ones(i: i32, nums: &[i32]) -> i32 {
        let mut count = nums[i as usize];
        if i > 0 {
            count += nums[(i - 1) as usize];
        }
        if (i + 1) < nums.len() as i32 {
            count += nums[(i + 1) as usize];
        }
        count
    }

    pub fn minimum_moves(nums: Vec<i32>, k: i32, max_changes: i32) -> i64 {
        let n = nums.len() as i32;

        // 前缀和数组：sum[i+1] 表示 nums[0..=i] 中 1 的个数
        let mut prefix_count = vec![0i64; (n + 1) as usize];
        // 前缀和数组：index_sum[i+1] 表示 nums[0..=i] 中所有 1 的索引之和
        let mut prefix_index_sum = vec![0i64; (n + 1) as usize];

        for i in 0..n {
            let idx = i as usize;
            prefix_count[idx + 1] = prefix_count[idx] + nums[idx] as i64;
            prefix_index_sum[idx + 1] = prefix_index_sum[idx] + (nums[idx] as i64) * (i as i64);
        }

        let mut answer = i64::MAX;

        // 枚举每一个可能的起始位置 i
        for i in 0..n {
            let nearby_ones = Self::count_nearby_ones(i, &nums);

            // 情况1：利用 Alice 初始位置及其相邻位置的 1，加上 max_changes 次修改
            if nearby_ones + max_changes >= k {
                if k <= nearby_ones {
                    // 只需从初始位置拾取，无需移动
                    answer = answer.min((k - nums[i as usize]) as i64);
                } else {
                    // 需要额外通过修改获得 1，每次修改代价为 2
                    answer = answer.min((2 * k - nearby_ones - nums[i as usize]) as i64);
                }
                continue;
            }

            // 情况2：需要从更远的位置收集 1
            // 二分查找最小的半径 left，使得区间 [i-left, i+left] 内至少有 (k - max_changes) 个 1
            let mut left = 0;
            let mut right = n;

            while left <= right {
                let mid = (left + right) / 2;
                let l = (i - mid).max(0);
                let r = (i + mid).min(n - 1);

                let ones_in_range = prefix_count[(r + 1) as usize] - prefix_count[l as usize];
                if ones_in_range >= (k - max_changes) as i64 {
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            }

            // 确定最终的区间 [l, r]，其中包含至少 (k - max_changes) 个 1
            let mut l = (i - left).max(0);
            let r = (i + left).min(n - 1);

            // 如果区间内 1 的数量超过需求，向右收缩左边界
            if prefix_count[(r + 1) as usize] - prefix_count[l as usize] > (k - max_changes) as i64 {
                l += 1;
            }

            // 计算区间内位于 i 左侧和右侧的 1 的数量
            let left_count = prefix_count[(i + 1) as usize] - prefix_count[l as usize];
            let right_count = prefix_count[(r + 1) as usize] - prefix_count[(i + 1) as usize];

            // 将所有 1 移动到位置 i 的代价：
            // 右侧的 1 向左移动：index_sum[r+1] - index_sum[i+1] - i * right_count
            // 左侧的 1 向右移动：i * left_count - (index_sum[i+1] - index_sum[l])
            // 再加上通过修改获得的 (max_changes) 个 1，每个代价 2
            let cost = (prefix_index_sum[(r + 1) as usize] - prefix_index_sum[(i + 1) as usize])
                - (i as i64) * right_count
                + (i as i64) * left_count
                - (prefix_index_sum[(i + 1) as usize] - prefix_index_sum[l as usize])
                + (2 * max_changes as i64);

            answer = answer.min(cost);
        }

        answer
    }
}
```
