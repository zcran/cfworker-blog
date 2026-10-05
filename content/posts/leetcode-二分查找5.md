---
title: "leetcode-二分查找5"
date: 2026-09-28T12:29:10+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 搜索旋转排序数组 II

已知存在一个按非降序排列的整数数组 nums ，数组中的值不必互不相同。

在传递给函数之前，nums 在预先未知的某个下标 k（0 <= k < nums.length）上进行了 旋转 ，使数组变为 [nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]（下标 从 0 开始 计数）。例如， [0,1,2,4,4,4,5,6,6,7] 在下标 5 处经旋转后可能变为 [4,5,6,6,7,0,1,2,4,4] 。

给你 旋转后 的数组 nums 和一个整数 target ，请你编写一个函数来判断给定的目标值是否存在于数组中。如果 nums 中存在这个目标值 target ，则返回 true ，否则返回 false 。

你必须尽可能减少整个操作步骤。


```
impl Solution {
    pub fn search(nums: Vec<i32>, target: i32) -> bool {
        let (mut left, mut right) = (0, nums.len() as i32 - 1);

        while left <= right {
            let mid = left + (right - left) / 2;
            let (l, m, r) = (
                nums[left as usize],
                nums[mid as usize],
                nums[right as usize],
            );

            if m == target {
                return true;
            }

            // 无法判断哪一段有序，缩小区间
            if l == m && m == r {
                left += 1;
                right -= 1;
            } else if l <= m {
                // 左半段 [left, mid] 有序
                if l <= target && target < m {
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            } else {
                // 右半段 [mid, right] 有序
                if m < target && target <= r {
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            }
        }
        false
    }
}
```
