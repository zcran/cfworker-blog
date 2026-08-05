---
title: "leetcode-滑动窗口40"
date: 2026-07-18T11:02:31+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 删除子数组的最大得分

给你一个正整数数组 nums ，请你从中删除一个含有 若干不同元素 的子数组。删除子数组的 得分 就是子数组各元素之 和 。

返回 只删除一个 子数组可获得的 最大得分 。

如果数组 b 是数组 a 的一个连续子序列，即如果它等于 a[l],a[l+1],...,a[r] ，那么它就是 a 的一个子数组。


```
impl Solution {
    pub fn maximum_unique_subarray(nums: Vec<i32>) -> i32 {
        use std::collections::HashMap;

        let n = nums.len();
        let mut prefix_sum = vec![0; n + 1];
        for i in 0..n {
            prefix_sum[i + 1] = prefix_sum[i] + nums[i];
        }

        let mut last_pos = HashMap::new();
        let mut left = 0;
        let mut max_sum = 0;

        for right in 0..n {
            let val = nums[right];

            // 如果当前元素出现过，且位置在窗口内，移动左边界
            if let Some(&pos) = last_pos.get(&val) {
                left = left.max(pos + 1);
            }

            // 更新当前元素的最新位置
            last_pos.insert(val, right);

            // 计算当前窗口和，更新最大值
            let cur_sum = prefix_sum[right + 1] - prefix_sum[left];
            max_sum = max_sum.max(cur_sum);
        }

        max_sum
    }
}
```
