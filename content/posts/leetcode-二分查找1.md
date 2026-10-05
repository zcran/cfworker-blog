---
title: "leetcode-二分查找1"
date: 2026-09-28T12:29:10+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 搜索旋转排序数组

整数数组 nums 按升序排列，数组中的值 互不相同 。

在传递给函数之前，nums 在预先未知的某个下标 k（0 <= k < nums.length）上进行了 向左旋转，使数组变为 [nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]（下标 从 0 开始 计数）。例如， [0,1,2,4,5,6,7] 下标 3 上向左旋转后可能变为 [4,5,6,7,0,1,2] 。

给你 旋转后 的数组 nums 和一个整数 target ，如果 nums 中存在这个目标值 target ，则返回它的下标，否则返回 -1 。

你必须设计一个时间复杂度为 O(log n) 的算法解决此问题。


```
impl Solution {
    pub fn search(nums: Vec<i32>, target: i32) -> i32 {
        let n = nums.len();
        let last = nums[n - 1];
        let (mut left, mut right) = (0, n - 1); // 左闭右开区间 [left, right)

        // 二分查找：先根据 target 与 last 的关系判断目标位于哪一段，
        // 再在该段内进行普通二分。
        while left < right {
            let mid = left + (right - left) / 2;
            let x = nums[mid];

            if (target > last) == (x > last) {
                // x 和 target 在同一段，按普通二分处理
                if x >= target {
                    right = mid;
                } else {
                    left = mid + 1;
                }
            } else if target > last {
                // target 在第一段，x 在第二段，去左边找
                right = mid;
            } else {
                // x 在第一段，target 在第二段，去右边找
                left = mid + 1;
            }
        }

        if nums[left] == target { left as i32 } else { -1 }
    }
}
```
