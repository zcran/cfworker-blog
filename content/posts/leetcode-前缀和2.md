---
title: "leetcode-前缀和2"
date: 2026-08-25T10:02:46+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 分割数组的最大值

给定一个非负整数数组 nums 和一个整数 k ，你需要将这个数组分成 k 个非空的连续子数组，使得这 k 个子数组各自和的最大值 最小。

返回分割后最小的和的最大值。

子数组 是数组中连续的部分。


```
/// 分割数组的最大和最小化
///
/// 核心思路：二分答案 + 贪心验证
///
/// 1. 答案范围：[max(nums), sum(nums)]
/// 2. 对于给定的 mid，贪心地划分数组：
///    - 尽可能使每个子数组和不超过 mid
///    - 如果需要的分段数 ≤ k，说明 mid 可行
///    - 否则 mid 不可行
/// 3. 二分查找最小的可行 mid
///
/// 时间复杂度 O(n log sum)，空间复杂度 O(1)
impl Solution {
    pub fn split_array(nums: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;
        let n = nums.len();

        // 如果只有一个元素或只分一段，直接返回
        if n == 1 || k == 1 {
            return nums.iter().sum();
        }

        // 计算二分边界
        let mut left = *nums.iter().max().unwrap(); // 下界：最大元素
        let mut right = nums.iter().sum::<i32>();   // 上界：所有元素之和

        // 二分查找最小的可行值
        while left < right {
            let mid = left + (right - left) / 2;

            if Self::can_split(&nums, k, mid) {
                right = mid;  // mid 可行，尝试更小值
            } else {
                left = mid + 1; // mid 不可行，需要更大值
            }
        }

        left
    }

    /// 检查能否将数组分成 k 段，使得每段和都不超过 max_sum
    fn can_split(nums: &[i32], k: usize, max_sum: i32) -> bool {
        let mut count = 1;      // 当前分段数
        let mut current_sum = 0; // 当前段的和

        for &num in nums {
            // 如果单个元素超过 max_sum，直接返回 false
            if num > max_sum {
                return false;
            }

            // 尝试将当前元素加入当前段
            if current_sum + num <= max_sum {
                current_sum += num;
            } else {
                // 当前段已满，开启新段
                count += 1;
                if count > k {
                    return false; // 分段数超过 k
                }
                current_sum = num;
            }
        }

        true // 可以分成 ≤ k 段
    }
}
```
