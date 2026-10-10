---
title: "leetcode-哈希表95"
date: 2026-10-09T20:08:02+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 数对和受限的最长子数组

给你一个整数数组 nums。

如果不存在三个 互不相同 的下标 i、j 和 k，满足 l <= i, j, k <= r 且：

nums[i] + nums[j] == nums[k]

则 子数组 nums[l..r] 是 有效 子数组。

返回 nums 中有效子数组的 最大 长度。

子数组 是数组中一个连续 非空 元素序列。


```
impl Solution {
    pub fn max_subarray(nums: Vec<i32>) -> i32 {
        let mx = *nums.iter().max().unwrap() as usize;

        // cnt_sum[v] 记录当前窗口内有多少对元素之和为 v
        // cnt_diff[v] 记录当前窗口内有多少对元素之差（绝对值）为 v
        let mut cnt_sum = vec![0i32; mx * 2 + 1];
        let mut cnt_diff = vec![0i32; mx + 1];

        let mut left = 0usize;
        let mut ans = 0usize;

        // 枚举右端点 i，维护满足条件的最大窗口 [left, i]
        for i in 0..nums.len() {
            let x = nums[i] as usize;

            // 若窗口内存在两数之和/差等于 x，则不满足有效条件，需收缩左边界
            while cnt_sum[x] > 0 || cnt_diff[x] > 0 {
                let y = nums[left] as usize;
                left += 1;
                // 移除 y 与窗口中其余元素构成的数对
                for j in left..i {
                    let z = nums[j] as usize;
                    cnt_sum[y + z] -= 1;
                    cnt_diff[y.abs_diff(z)] -= 1;
                }
            }

            // 将 x 与窗口中其余元素构成的数对加入计数
            for j in left..i {
                let y = nums[j] as usize;
                cnt_sum[x + y] += 1;
                cnt_diff[x.abs_diff(y)] += 1;
            }

            ans = ans.max(i - left + 1);
        }

        ans as i32
    }
}
```
