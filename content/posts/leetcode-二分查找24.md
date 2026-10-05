---
title: "leetcode-二分查找24"
date: 2026-09-28T12:29:11+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 山脉数组的峰顶索引

给定一个长度为 n 的整数 山脉 数组 arr ，其中的值递增到一个 峰值元素 然后递减。

返回峰值元素的下标。

你必须设计并实现时间复杂度为 O(log(n)) 的解决方案。


```
impl Solution {
    /// 返回山脉数组中峰值元素的下标。
    ///
    /// 山脉数组先递增后递减，峰值是唯一的局部最大值。
    /// 比较 arr[mid] 与 arr[mid+1]：
    ///   - arr[mid] < arr[mid+1]：处于上升段，峰值在右侧，lo = mid+1
    ///   - arr[mid] > arr[mid+1]：处于下降段，峰值在左侧或就是 mid，hi = mid
    /// 最终 lo == hi 即为峰值下标。
    pub fn peak_index_in_mountain_array(arr: Vec<i32>) -> i32 {
        let (mut lo, mut hi) = (0, arr.len() - 1);
        while lo < hi {
            let mid = lo + (hi - lo) / 2;
            if arr[mid] < arr[mid + 1] {
                lo = mid + 1;
            } else {
                hi = mid;
            }
        }
        lo as i32
    }
}
```
