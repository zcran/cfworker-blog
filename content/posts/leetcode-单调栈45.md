---
title: "leetcode-单调栈45"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 最多 K 个元素的子数组的最值之和

给你一个整数数组 nums 和一个 正 整数 k 。 返回 最多 有 k 个元素的所有子数组的 最大 和 最小 元素之和。

子数组 是数组中的一个连续、非空 的元素序列。

```
impl Solution {
    pub fn min_max_subarray_sum(nums: Vec<i32>, k: i32) -> i64 {
        let n = nums.len();
        let k = k as usize;

        // 计算最大值之和
        let max_sum = Self::sum_of_subarray_max(nums.as_slice(), k);
        // 计算最小值之和
        let min_sum = Self::sum_of_subarray_min(nums.as_slice(), k);

        max_sum + min_sum
    }

    /// 计算所有长度 <= k 的子数组的最大值之和
    fn sum_of_subarray_max(nums: &[i32], k: usize) -> i64 {
        let n = nums.len();
        let mut ans = 0i64;
        let mut stack = Vec::new();

        for i in 0..n {
            // 维护单调递减栈，弹出 <= 当前值的元素
            while let Some(&j) = stack.last() {
                if nums[j] <= nums[i] {
                    stack.pop();
                } else {
                    break;
                }
            }

            // 左侧边界：左边第一个更大的位置
            let left = stack.last().map_or(0, |&j| j + 1);
            stack.push(i);

            // 右侧边界：右边第一个更大的位置
            // 需要从右向左再遍历一次
        }

        // 先计算右侧边界
        let mut right = vec![n - 1; n];
        stack.clear();
        for i in (0..n).rev() {
            while let Some(&j) = stack.last() {
                if nums[j] <= nums[i] {
                    stack.pop();
                } else {
                    break;
                }
            }
            right[i] = stack.last().map_or(n - 1, |&j| j - 1);
            stack.push(i);
        }

        // 重新遍历计算左侧边界和贡献
        let mut left = vec![0; n];
        stack.clear();
        for i in 0..n {
            while let Some(&j) = stack.last() {
                if nums[j] < nums[i] {  // 左侧严格小于，避免重复
                    stack.pop();
                } else {
                    break;
                }
            }
            left[i] = stack.last().map_or(0, |&j| j + 1);
            stack.push(i);
        }

        // 计算每个元素作为最大值的贡献
        for i in 0..n {
            let val = nums[i] as i64;
            let left_len = i - left[i];      // 左侧可选元素个数
            let right_len = right[i] - i;    // 右侧可选元素个数

            // 统计包含 i 且长度 <= k 的子数组数量
            let cnt = Self::count_subarrays_with_limit(left_len, right_len, k);
            ans += cnt * val;
        }

        ans
    }

    /// 计算所有长度 <= k 的子数组的最小值之和
    fn sum_of_subarray_min(nums: &[i32], k: usize) -> i64 {
        let n = nums.len();
        let mut ans = 0i64;
        let mut stack = Vec::new();

        // 计算右侧边界（右侧第一个更小的位置）
        let mut right = vec![n - 1; n];
        for i in (0..n).rev() {
            while let Some(&j) = stack.last() {
                if nums[j] >= nums[i] {
                    stack.pop();
                } else {
                    break;
                }
            }
            right[i] = stack.last().map_or(n - 1, |&j| j - 1);
            stack.push(i);
        }

        // 计算左侧边界（左侧第一个更小的位置）
        let mut left = vec![0; n];
        stack.clear();
        for i in 0..n {
            while let Some(&j) = stack.last() {
                if nums[j] > nums[i] {  // 左侧严格大于，避免重复
                    stack.pop();
                } else {
                    break;
                }
            }
            left[i] = stack.last().map_or(0, |&j| j + 1);
            stack.push(i);
        }

        // 计算每个元素作为最小值的贡献
        for i in 0..n {
            let val = nums[i] as i64;
            let left_len = i - left[i];
            let right_len = right[i] - i;
            let cnt = Self::count_subarrays_with_limit(left_len, right_len, k);
            ans += cnt * val;
        }

        ans
    }

    /// 统计包含当前位置且长度 <= k 的子数组数量
    ///
    /// left: 左侧可选元素个数（不含当前位置）
    /// right: 右侧可选元素个数（不含当前位置）
    /// k: 最大子数组长度
    ///
    /// 问题转化为：选择 x ∈ [0, left], y ∈ [0, right]，且 x + y + 1 <= k
    /// 即 x + y <= k - 1
    fn count_subarrays_with_limit(left: usize, right: usize, k: usize) -> i64 {
        let t = k - 1;  // 还可以选择的元素总数
        let t1 = left.min(t);
        let t2 = right.min(t);

        if t1 + t2 <= t {
            // 情况1：所有组合都满足长度限制
            ((t1 + 1) * (t2 + 1)) as i64
        } else {
            // 情况2：需要排除 x + y > t 的组合
            // 使用公式：总数 - 超出部分
            // 或者直接计算合法组合数
            let mut total = 0i64;
            for x in 0..=t1 {
                let max_y = (t - x).min(t2);
                if max_y >= 0 {
                    total += (max_y + 1) as i64;
                }
            }
            total
        }
    }
}
```
