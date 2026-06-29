---
title: "leetcode-分治18"
date: 2026-06-13T10:51:38+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 排序数组

给你一个整数数组 nums，请你将该数组升序排列。

你必须在 不使用任何内置函数 的情况下解决问题，时间复杂度为 O(nlog(n))，并且空间复杂度尽可能小。

```
use rand::Rng;

impl Solution {
    // 在 nums 中随机选择一个基准元素 pivot
    // 根据 pivot 重新排列 nums
    // 重新排列后，<= pivot 的元素都在 pivot 的左侧，>= pivot 的元素都在 pivot 的右侧
    // 返回 pivot 在重新排列后的 nums 中的下标
    // 特别地，如果数组的所有元素都等于 pivot，我们会返回数组的中心下标，避免退化
    fn partition(nums: &mut [i32]) -> usize {
        // 1. 在 nums 中随机选择一个基准元素 pivot
        let n = nums.len();
        let mut rng = rand::thread_rng();
        let i = rng.gen_range(0..n);
        let pivot = nums[i];
        // 把 pivot 与第一个元素交换，避免 pivot 干扰后续划分，从而简化实现逻辑
        nums.swap(i, 0);

        // 2. 相向双指针遍历 [1, n-1]
        // 循环不变量：在循环过程中，数组的数据分布始终如下图
        // [ pivot | <=pivot | 尚未遍历 | >=pivot ]
        //   ^                 ^     ^         ^
        //   0                 i     j         n-1

        let mut i = 1;
        let mut j = n - 1;
        loop {
            while i <= j && nums[i] < pivot {
                i += 1;
            }
            // 此时 nums[i] >= pivot

            while i <= j && nums[j] > pivot {
                j -= 1;
            }
            // 此时 nums[j] <= pivot

            if i >= j {
                break;
            }

            // 维持循环不变量
            nums.swap(i, j);
            i += 1;
            j -= 1;
        }

        // 循环结束后
        // [ pivot | <=pivot | >=pivot ]
        //   ^             ^   ^     ^
        //   0             j   i     n-1

        // 3. 把 pivot 与 nums[j] 交换，完成划分（partition）
        // 为什么与 j 交换？
        // 如果与 i 交换，可能会出现 i = n 的情况，已经下标越界了，无法交换
        // 另一个原因是如果 nums[i] > pivot，交换会导致一个大于 pivot 的数出现在子数组最左边，不是有效划分
        // 与 j 交换，即使 j = 0，交换也不会出错
        nums.swap(0, j);

        // 交换后
        // [ <=pivot | pivot | >=pivot ]
        //               ^
        //               j

        // 返回 pivot 的下标
        j
    }

    // 快速排序 nums
    fn quick_sort(nums: &mut [i32]) {
        // 优化：如果 nums 已是升序，直接返回
        if nums.windows(2).all(|w| w[0] <= w[1]) {
            return;
        }

        let i = Self::partition(nums);
        Self::quick_sort(&mut nums[..i]); // 排序在 pivot 左侧的元素
        Self::quick_sort(&mut nums[i + 1..]); // 排序在 pivot 右侧的元素
    }

    pub fn sort_array(mut nums: Vec<i32>) -> Vec<i32> {
        Self::quick_sort(&mut nums);
        nums
    }
}

```
