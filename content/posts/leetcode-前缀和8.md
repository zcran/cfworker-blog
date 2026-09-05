---
title: "leetcode-前缀和8"
date: 2026-08-25T10:02:46+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 得分最高的最小轮调

给你一个数组 nums，我们可以将它按一个非负整数 k 进行轮调，这样可以使数组变为 [nums[k], nums[k + 1], ... nums[nums.length - 1], nums[0], nums[1], ..., nums[k-1]] 的形式。此后，任何值小于或等于其索引的项都可以记作一分。

例如，数组为 nums = [2,4,1,3,0]，我们按 k = 2 进行轮调后，它将变成 [1,3,0,2,4]。这将记为 3 分，因为 1 > 0 [不计分]、3 > 1 [不计分]、0 <= 2 [计 1 分]、2 <= 3 [计 1 分]，4 <= 4 [计 1 分]。

在所有可能的轮调中，返回我们所能得到的最高分数对应的轮调下标 k 。如果有多个答案，返回满足条件的最小的下标 k 。


```
/// 寻找最佳轮调下标
///
/// 核心思路：差分数组
///
/// 对于每个元素 nums[i]，轮调 k 次后的位置为：(i - k + n) % n
/// 能得分条件：nums[i] <= (i - k + n) % n
///
/// 对于每个 i，满足条件的 k 形成一个连续区间
/// 使用差分数组记录每个 k 的得分变化
///
/// 时间复杂度 O(n)，空间复杂度 O(n)
impl Solution {
    pub fn best_rotation(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let mut diff = vec![0; n];

        for (i, &num) in nums.iter().enumerate() {
            let x = num as usize;

            // 如果 x >= n，永远无法得分
            if x >= n {
                continue;
            }

            // 轮调 k 次后位置：(i - k + n) % n
            // 需要：x <= (i - k + n) % n

            // 情况1：当 i >= x 时
            // k ∈ [0, i - x] 可以得分
            // 情况2：当 i < x 时
            // k ∈ [i + 1, i + n - x] 可以得分

            if i >= x {
                // 区间1：[0, i - x]
                diff[0] += 1;
                if i - x + 1 < n {
                    diff[i - x + 1] -= 1;
                }

                // 区间2：[i + 1, n - 1]（当 i + 1 < n 时）
                if i + 1 < n {
                    diff[i + 1] += 1;
                }
            } else {
                // 区间：[i + 1, i + n - x]
                if i + 1 < n {
                    diff[i + 1] += 1;
                }
                let end = i + n - x;
                if end + 1 < n {
                    diff[end + 1] -= 1;
                }
            }
        }

        // 扫描差分数组，找出最大得分对应的轮调次数
        let mut score = 0;
        let mut max_score = 0;
        let mut best_k = 0;

        for k in 0..n {
            score += diff[k];
            if score > max_score {
                max_score = score;
                best_k = k;
            }
        }

        best_k as i32
    }
}
```
