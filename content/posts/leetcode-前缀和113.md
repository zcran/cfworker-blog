---
title: "leetcode-前缀和113"
date: 2026-08-25T10:02:53+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 长度至少为 M 的 K 个子数组之和

给你一个整数数组 nums 和两个整数 k 和 m。

返回数组 nums 中 k 个不重叠子数组的 最大 和，其中每个子数组的长度 至少 为 m。

子数组 是数组中的一个连续序列。


```
impl Solution {
    pub fn max_sum(nums: Vec<i32>, k: i32, m: i32) -> i32 {
        let (k, m) = (k as usize, m as usize);
        let n = nums.len();

        // 计算前缀和，prefix[i] 表示 nums[0..i) 的和
        let mut prefix = vec![0; n + 1];
        for i in 0..n {
            prefix[i + 1] = prefix[i] + nums[i];
        }

        // dp[i][j] 表示从前 j 个元素中选 i 个子数组的最大和
        // 使用一维数组滚动优化，dp[i][j] 只依赖 dp[i-1] 和 dp[i][j-1]
        let mut dp_prev = vec![0; n + 1];
        let mut dp_curr = vec![0; n + 1];

        for i in 1..=k {
            // 初始化为极小值，表示不可达状态
            dp_curr.fill(i32::MIN);
            let mut max_val = i32::MIN;

            // j 至少为 i*m，且要给后续子数组留空间
            // 左边界：至少 i*m 个元素；右边界：最多 n - (k-i)*m
            let left_bound = i * m;
            let right_bound = n - (k - i) * m;

            for j in left_bound..=right_bound {
                // 候选：在位置 j-m 前选 i-1 个子数组，然后加上从 j-m 到 j 的子数组
                // 更新 max_val = max(f[i-1][j-m] - prefix[j-m])
                let candidate = dp_prev[j - m] - prefix[j - m];
                if candidate > max_val {
                    max_val = candidate;
                }

                // 当前 dp[i][j] = max(不选第 j 个元素, 选以 j 结尾的子数组)
                // 不选: dp_curr[j-1]，选: max_val + prefix[j]
                dp_curr[j] = dp_curr[j - 1].max(max_val + prefix[j]);
            }

            // 交换当前行和上一行
            std::mem::swap(&mut dp_prev, &mut dp_curr);
        }

        // dp_prev 现在存储的是 dp[k][*]
        dp_prev[n]
    }
}
```
