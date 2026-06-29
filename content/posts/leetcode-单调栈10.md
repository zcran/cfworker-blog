---
title: "leetcode-单调栈10"
date: 2026-06-07T10:14:04+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 最短无序连续子数组

给你一个整数数组 nums ，你需要找出一个 连续子数组 ，如果对这个子数组进行升序排序，那么整个数组都会变为升序排序。

请你找出符合题意的 最短 子数组，并输出它的长度。

```
impl Solution {
    /// 寻找最短无序连续子数组
    ///
    /// 核心思路：
    /// 1. 从左到右找最后一个违反升序的位置（右侧边界）
    /// 2. 从右到左找最后一个违反降序的位置（左侧边界）
    /// 3. 边界之间的子数组即为需要排序的部分
    pub fn find_unsorted_subarray(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        if n <= 1 { return 0; }

        // 第一步：从左到右，找到右边界
        // 右边界是最后一个使数组不是升序的位置
        let mut max_left = nums[0];
        let mut right = 0;
        for i in 1..n {
            if nums[i] < max_left {
                right = i;  // 出现降序，更新右边界
            } else {
                max_left = nums[i];
            }
        }

        if right == 0 { return 0; }  // 已经完全有序

        // 第二步：从右到左，找到左边界
        // 左边界是最后一个使数组不是降序的位置
        let mut min_right = nums[n - 1];
        let mut left = n - 1;
        for i in (0..n - 1).rev() {
            if nums[i] > min_right {
                left = i;  // 出现升序，更新左边界
            } else {
                min_right = nums[i];
            }
        }

        (right - left + 1) as i32
    }
}
```
