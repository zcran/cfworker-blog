---
title: "leetcode-动态规划158"
date: 2026-10-05T20:40:05+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 三段式数组 II

给你一个长度为 n 的整数数组 nums。

三段式子数组 是一个连续子数组 nums[l...r]（满足 0 <= l < r < n），并且存在下标 l < p < q < r，使得：

nums[l...p] 严格 递增，
nums[p...q] 严格 递减，
nums[q...r] 严格 递增。

请你从数组 nums 的所有三段式子数组中找出和最大的那个，并返回其 最大 和。


```
impl Solution {
    pub fn max_sum_trionic(nums: Vec<i32>) -> i64 {
        let n = nums.len();
        let mut ans = i64::MIN;
        let mut i = 0;

        while i < n {
            // 寻找第一段严格递增的终点 p
            let mut j = i + 1;
            while j < n && nums[j - 1] < nums[j] {
                j += 1;
            }
            let p = j - 1;
            if p == i {
                // 第一段长度至少为 2，否则跳过
                i += 1;
                continue;
            }

            // 累加第一段（含峰值 p），随后寻找第二段严格递减的终点 q
            let mut res = (nums[p] + nums[p - 1]) as i64;
            while j < n && nums[j - 1] > nums[j] {
                res += nums[j] as i64;
                j += 1;
            }
            let q = j - 1;
            // 第二段长度至少为 2，且 q 不能是末尾，且 q+1 必须严格大于 q 才能开始第三段
            if q == p || q == n - 1 || (j < n && nums[j] <= nums[q]) {
                i = q;
                continue;
            }

            // 第三段：从 q+1 开始严格递增，求后缀累加和的最大值
            res += nums[q + 1] as i64;
            let mut max_sum = 0i64;
            let mut sum = 0i64;
            let mut k = q + 2;
            while k < n && nums[k] > nums[k - 1] {
                sum += nums[k] as i64;
                if sum > max_sum {
                    max_sum = sum;
                }
                k += 1;
            }
            res += max_sum;

            // 第一段：从 p-1 向左严格递减（即反向递增），求前缀累加和的最大值
            max_sum = 0;
            sum = 0;
            let mut k = p as isize - 2;
            while k >= i as isize {
                sum += nums[k as usize] as i64;
                if sum > max_sum {
                    max_sum = sum;
                }
                k -= 1;
            }
            res += max_sum;

            ans = ans.max(res);
            // 下一轮的起点可回退到 q-1，因为 q-1 可能是新的递增起点
            i = q - 1;
        }

        ans
    }
}
```
