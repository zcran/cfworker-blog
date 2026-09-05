---
title: "leetcode-堆75"
date: 2026-08-21T09:54:15+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 数组中的第 K 个最大元素

给定整数数组 nums 和整数 k，请返回数组中第 k 个最大的元素。

请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

/// 查找数组中第 k 个最大的元素
///
/// 方法：使用最小堆维护当前最大的 k 个元素
/// 时间复杂度 O(n log k)，空间复杂度 O(k)
/// 这是处理大规模数据的最优方案之一
impl Solution {
    pub fn find_kth_largest(nums: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;

        // 使用最小堆维护最大的 k 个元素
        // 堆顶就是第 k 个最大的元素
        let mut heap = BinaryHeap::with_capacity(k);

        for num in nums {
            if heap.len() < k {
                // 堆未满，直接入堆
                heap.push(Reverse(num));
            } else if num > heap.peek().unwrap().0 {
                // 当前元素比堆顶（第 k 大元素）大，替换
                heap.pop();
                heap.push(Reverse(num));
            }
            // 否则，当前元素小于等于第 k 大，忽略
        }

        // 堆顶就是第 k 个最大的元素
        heap.peek().unwrap().0
    }
}
```


```
/// 快速选择算法（平均时间复杂度 O(n)，最坏 O(n²)）
impl Solution {
    pub fn find_kth_largest(nums: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;
        let mut nums = nums;
        let len = nums.len();  // 先计算长度
        let target = len - k;

        quick_select(&mut nums, target, 0, len - 1)
    }
}

fn quick_select(nums: &mut [i32], k: usize, left: usize, right: usize) -> i32 {
    if left == right {
        return nums[left];
    }

    let pivot = partition(nums, left, right);

    if k == pivot {
        nums[pivot]
    } else if k < pivot {
        quick_select(nums, k, left, pivot - 1)
    } else {
        quick_select(nums, k, pivot + 1, right)
    }
}

fn partition(nums: &mut [i32], left: usize, right: usize) -> usize {
    let pivot = nums[right];
    let mut i = left;

    for j in left..right {
        if nums[j] <= pivot {
            nums.swap(i, j);
            i += 1;
        }
    }

    nums.swap(i, right);
    i
}
```
