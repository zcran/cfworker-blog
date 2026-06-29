---
title: "leetcode-分治32"
date: 2026-06-13T10:51:39+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 统计主要元素子数组数目 I

给你一个整数数组 nums 和一个整数 target。

返回数组 nums 中满足 target 是 主要元素 的 子数组 的数目。

一个子数组的 主要元素 是指该元素在该子数组中出现的次数 严格大于 其长度的 一半 。

子数组 是数组中的一段连续且 非空 的元素序列。

```
impl Solution {
    /// 计算数组中 target 作为主要元素的子数组数量
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n²)
    /// - 空间复杂度：O(1)
    pub fn count_majority_subarrays(nums: Vec<i32>, target: i32) -> i32 {
        let n = nums.len();
        let mut ans = 0;

        for i in 0..n {
            let mut cnt = 0;
            for j in i..n {
                if nums[j] == target {
                    cnt += 1;
                }
                if cnt * 2 > j - i + 1 {
                    ans += 1;
                }
            }
        }

        ans
    }
}
```
