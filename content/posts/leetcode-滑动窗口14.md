---
title: "leetcode-滑动窗口14"
date: 2026-07-18T11:02:30+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 找到 K 个最接近的元素

给定一个 排序好 的数组 arr ，两个整数 k 和 x ，从数组中找到最靠近 x（两数之差最小）的 k 个数。返回的结果必须要是按升序排好的。

整数 a 比整数 b 更接近 x 需要满足：

|a - x| < |b - x| 或者
|a - x| == |b - x| 且 a < b

```
impl Solution {
    pub fn find_closest_elements(arr: Vec<i32>, k: i32, x: i32) -> Vec<i32> {
        let k = k as usize;
        let n = arr.len();
        let mut left = 0;
        let mut right = n - k;

        // 二分查找最优的左边界
        while left < right {
            let mid = left + (right - left) / 2;
            // 比较 mid 和 mid+k 哪个更远离 x
            // 若 mid 更远，则左边界右移
            if x - arr[mid] > arr[mid + k] - x {
                left = mid + 1;
            } else {
                right = mid;
            }
        }

        arr[left..left + k].to_vec()
    }
}
```
