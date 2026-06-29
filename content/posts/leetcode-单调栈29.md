---
title: "leetcode-单调栈29"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 好子数组的最大分数

给你一个整数数组 nums （下标从 0 开始）和一个整数 k 。

一个子数组 (i, j) 的 分数 定义为 min(nums[i], nums[i+1], ..., nums[j]) * (j - i + 1) 。一个 好 子数组的两个端点下标需要满足 i <= k <= j 。

请你返回 好 子数组的最大可能 分数 。

```
impl Solution {
    /// 最大分数（双指针贪心扩展）
    ///
    /// 策略：始终向值更大的一侧扩展，因为这样才能保持较大的最小值
    pub fn maximum_score(nums: Vec<i32>, k: i32) -> i32 {
        let n = nums.len();
        let mut l = k as usize;
        let mut r = l;
        let mut min_val = nums[l];
        let mut ans = min_val;

        while l > 0 || r < n - 1 {
            // 比较左右两侧的值，选择较大的方向扩展
            if l == 0 {
                r += 1;
            } else if r == n - 1 {
                l -= 1;
            } else if nums[l - 1] > nums[r + 1] {
                l -= 1;
            } else {
                r += 1;
            }

            // 更新区间最小值（只需和新加入的元素比较）
            if nums[l] < min_val {
                min_val = nums[l];
            }
            if nums[r] < min_val {
                min_val = nums[r];
            }

            // 更新答案
            let len = (r - l + 1) as i32;
            if len * min_val > ans {
                ans = len * min_val;
            }
        }

        ans
    }
}
```
