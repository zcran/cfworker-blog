---
title: "leetcode-枚举97"
date: 2026-07-09T10:05:04+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 替换最多一个元素后的最长等差子数组

给你一个整数数组 nums。

如果子数组中相邻元素的差值是一个常数，那么这个子数组被称为 等差子数组。

你可以将 nums 中的 最多 一个元素替换为任意一个 整数。然后，从 nums 中选择一个等差子数组。

返回一个整数，该整数表示你可以选择的 最长 等差子数组的长度。

子数组 是数组中一段连续的元素序列。


```
impl Solution {
    pub fn longest_arithmetic(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        if n <= 2 {
            return n as i32;
        }

        // dp[i] 表示从 i 开始的最长等差子数组的终点和公差
        let mut dp_end = vec![0; n];
        let mut dp_diff = vec![0; n];
        dp_end[n - 2] = n - 1;
        dp_diff[n - 2] = nums[n - 1] - nums[n - 2];

        let mut ans = 2;
        for i in (0..=n - 3).rev() {
            let diff = nums[i + 1] - nums[i];
            dp_diff[i] = diff;
            if dp_diff[i + 1] == diff {
                dp_end[i] = dp_end[i + 1];
                ans = ans.max(dp_end[i] - i + 1);
            } else {
                dp_end[i] = i + 1;
            }
        }

        if ans == n {
            return ans as i32;
        }

        // 情况1：修改 nums[0]
        ans = ans.max(dp_end[1] - 0 + 1);

        if ans == n {
            return ans as i32;
        }

        // 情况2：修改 nums[n-1]
        let left = Self::find_left(&dp_end, &dp_diff, n - 3, dp_diff[n - 3]);
        ans = ans.max(n - left);

        if ans == n {
            return ans as i32;
        }

        // 情况3：修改中间元素
        for i in 1..n - 1 {
            if ans == n {
                break;
            }

            let left_diff = nums[i] - nums[i - 1];
            let right_diff = nums[i + 1] - nums[i];

            // 如果已经是等差，跳过
            if left_diff == right_diff {
                continue;
            }

            // 以左边公差为主：修改 nums[i]
            if i > 1 {
                let diff = nums[i - 1] - nums[i - 2];
                let new_val = nums[i - 1] + diff;
                let mut left = if i >= 3 {
                    Self::find_left(&dp_end, &dp_diff, i - 3, diff)
                } else {
                    i - 2
                };
                let mut right = i;

                if i + 1 < n && nums[i + 1] - new_val == diff {
                    right = if i + 2 < n && dp_diff[i + 1] == diff {
                        dp_end[i + 1]
                    } else {
                        i + 1
                    };
                }
                ans = ans.max(right - left + 1);
            }

            // 以右边公差为主：修改 nums[i]
            if i + 2 < n {
                let diff = nums[i + 2] - nums[i + 1];
                let new_val = nums[i + 1] - diff;
                let right = dp_end[i + 1];
                let mut left = i;

                if i > 0 && new_val - nums[i - 1] == diff {
                    left = if i >= 2 {
                        Self::find_left(&dp_end, &dp_diff, i - 2, diff)
                    } else {
                        i - 1
                    };
                }
                ans = ans.max(right - left + 1);
            }

            // 修改 nums[i] 同时连接左右
            if (nums[i + 1] - nums[i - 1]) % 2 == 0 {
                let diff = (nums[i + 1] - nums[i - 1]) / 2;
                let mut left = i - 1;
                let mut right = i + 1;

                if i + 2 < n && dp_diff[i + 1] == diff {
                    right = dp_end[i + 1];
                }
                if i >= 2 {
                    left = Self::find_left(&dp_end, &dp_diff, i - 2, diff);
                }
                ans = ans.max(right - left + 1);
            }
        }

        ans as i32
    }

    /// 向左扩展，找到最左边能延续公差 diff 的位置
    fn find_left(end: &[usize], diff: &[i32], mut cur: usize, target_diff: i32) -> usize {
        while cur > 0 && diff[cur] == target_diff {
            cur -= 1;
        }
        if cur == 0 && diff[0] == target_diff {
            return 0;
        }
        cur + 1
    }
}
```
