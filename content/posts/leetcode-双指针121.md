---
title: "leetcode-双指针121"
date: 2026-09-05T09:53:08+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 含上限元素的子序列和

给你一个大小为 n 的整数数组 nums 和一个正整数 k。

通过将每个元素 nums[i] 替换为 min(nums[i], x)，可以得到一个由值 x 限制（capped）的数组。

对于从 1 到 n 的每个整数 x，确定是否可以从由 x 限制的数组中选择一个 子序列，使所选元素的和 恰好 为 k。

返回一个下标从 0 开始的布尔数组 answer，其大小为 n，其中 answer[i] 为 true 表示当 x = i + 1 时可以选出满足要求的子序列；否则为 false。

子序列 是一个从数组中通过删除一些或不删除任何元素（且不改变剩余元素顺序）派生出来的 非空 数组。


```
impl Solution {
    pub fn subsequence_sum_after_capping(mut nums: Vec<i32>, k: i32) -> Vec<bool> {
        // 排序后，随着上限 x 增大，可用元素从最小的开始逐个“解锁”。
        // dp[s] 表示用当前已解锁的元素能否凑出和 s。
        nums.sort_unstable();
        let n = nums.len();
        let k = k as usize;

        let mut dp = vec![false; k + 1];
        dp[0] = true;

        // 当前已解锁的元素个数（即 nums[..unlocked] 可用）
        let mut unlocked = 0usize;
        let mut ans = vec![false; n];

        // 判断在“已解锁 unlocked 个元素 + 可额外任选若干个当前上限值 value”的情况下，
        // 能否凑出 k。
        // 因为当前上限为 value 时，所有未解锁元素都会被 cap 成 value，
        // 所以相当于在 dp 的基础上还能加入任意多个 value（最多未解锁个数个）。
        let can_reach = |dp: &[bool], unlocked: usize, value: usize| -> bool {
            let max_take = n - unlocked;
            // 只需检查加入 t 个 value 后能否补足 k
            // dp[k - t * value] 为 true 即可
            for t in 0..=max_take {
                let rem = match (t * value).checked_sub(k) {
                    // 这里写成 k - t*value 更直观，但避免减法下溢
                    _ => {
                        let prod = t * value;
                        if prod > k {
                            break;
                        }
                        k - prod
                    }
                };
                if dp[rem] {
                    return true;
                }
            }
            false
        };

        // x 从 1 到 n
        for x in 1..=n {
            let value = x; // 当前上限值

            // 解锁所有等于当前上限的元素：当 x 增大到某个 nums[i] 时，该元素不再被 cap
            while unlocked < n && nums[unlocked] as usize <= value {
                let v = nums[unlocked] as usize;
                // 0/1 背包：逆序更新
                for s in (v..=k).rev() {
                    if dp[s - v] {
                        dp[s] = true;
                    }
                }
                unlocked += 1;
            }

            ans[x - 1] = can_reach(&dp, unlocked, value);
        }

        ans
    }
}
```
