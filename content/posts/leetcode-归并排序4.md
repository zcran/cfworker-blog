---
title: "Leetcode 归并排序4"
date: 2025-05-23T21:22:00+08:00
tags: ["leetcode", "归并排序"]
draft: false
---

## 区间和的个数

给你一个整数数组 nums 以及两个整数 lower 和 upper 。求数组中，值位于范围 [lower, upper] （包含 lower 和 upper）之内的 区间和的个数 。

区间和 S(i, j) 表示在 nums 中，位置从 i 到 j 的元素之和，包含 i 和 j (i ≤ j)。

 

示例 1：
输入：nums = [-2,5,-1], lower = -2, upper = 2
输出：3
解释：存在三个区间：[0,0]、[2,2] 和 [0,2] ，对应的区间和分别是：-2 、-1 、2 。
示例 2：

输入：nums = [0], lower = 0, upper = 0
输出：1

```
impl Solution {
    pub fn count_range_sum(mut nums: Vec<i32>, lower: i32, upper: i32) -> i32 {
        let mut nums = nums.into_iter().map(|x| x as i64).collect::<Vec<i64>>();  // 将输入的 i32 数组转换为 i64 数组，防止求和时溢出。
        let mut count = 0;
        for i in 1..nums.len() {
            nums[i] += nums[i - 1];
        }       // 将数组转换为前缀和数组。例如，原数组 [1, 2, 3] 变为前缀和数组 [1, 3, 6]，其中 nums[i] 表示原数组前 i+1 个元素的和。
        nums.insert(0, 0);  // 在前缀和数组前插入一个 0，表示空数组的和。这使得子数组 [i..j] 的和可以直接用 nums[j] - nums[i] 表示。
        Solution::sort(&mut nums, lower as i64, upper as i64, &mut count);
        count  // 调用归并排序函数 sort 处理前缀和数组，同时统计满足条件的子数组数量。最后返回计数器的值。
    }

    fn sort(nums: &mut [i64], lower: i64, upper: i64, count: &mut i32) {
        if nums.len() <= 1 {
            return;
        }
        let mid = nums.len() / 2;
        Solution::sort(&mut nums[..mid], lower, upper, count);
        Solution::sort(&mut nums[mid..], lower, upper, count);
        Solution::merge(nums, mid, lower, upper, count);
    }

    fn merge(nums: &mut [i64], mid: usize, lower: i64, upper: i64, count: &mut i32) {
        // 滑动窗口
        let (mut start, mut end) = (mid, mid);
        for i in 0..mid {
            while start < nums.len() && nums[start] - nums[i] < lower {
                start += 1;
            }
            while end < nums.len() && nums[end] - nums[i] <= upper {
                end += 1;
            }
            *count += (end - start) as i32;
        }

        let mut tmp = Vec::with_capacity(nums.len());
        let (mut i, mut j) = (0, mid);
        while i < mid && j < nums.len() {
            if nums[i] <= nums[j] {
                tmp.push(nums[i]);
                i += 1;
            } else {
                tmp.push(nums[j]);
                j += 1;
            }
        }
        nums.iter().take(mid).skip(i).for_each(|x| {
            tmp.push(*x);
        });
        nums.iter().skip(j).for_each(|x| {
            tmp.push(*x);
        });
        nums.copy_from_slice(&tmp);
    }
}
```

#### 核心统计逻辑：
对于左半部分的每个索引 i，在右半部分中找到满足 lower ≤ nums[j] - nums[i] ≤ upper 的所有索引 j 的范围 [start, end)。
使用双指针 start 和 end 维护这个范围：
start 指向第一个满足 nums[j] - nums[i] ≥ lower 的位置。
end 指向第一个满足 nums[j] - nums[i] > upper 的位置。
区间长度 end - start 即为以 i 开头且满足条件的子数组数量。

#### 标准归并过程：
创建临时数组 tmp，将左右两部分按升序合并。
合并后将 tmp 复制回原数组，完成排序。

#### 算法总结

时间复杂度：O (n log n)，其中 n 是数组长度。归并排序的时间复杂度为 O (n log n)，每次合并时的滑动窗口操作时间复杂度为 O (n)。
空间复杂度：O (n)，用于存储前缀和数组和临时数组。