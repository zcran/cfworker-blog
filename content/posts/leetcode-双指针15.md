---
title: "leetcode-双指针15"
date: 2026-09-05T09:53:01+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 两数之和 II - 输入有序数组

给你一个下标从 1 开始的整数数组 numbers ，该数组已按 非递减顺序排列  ，请你从数组中找出满足相加之和等于目标数 target 的两个数。如果设这两个数分别是 numbers[index1] 和 numbers[index2] ，则 1 <= index1 < index2 <= numbers.length 。

以长度为 2 的整数数组 [index1, index2] 的形式返回这两个整数的下标 index1 和 index2。

你可以假设每个输入 只对应唯一的答案 ，而且你 不可以 重复使用相同的元素。

你所设计的解决方案必须只使用常量级的额外空间。


```
impl Solution {
    /// 在有序数组中查找两数之和等于目标值（双指针法）
    /// 时间复杂度 O(n)，空间复杂度 O(1)
    /// 利用数组有序特性，左右指针向中间移动
    pub fn two_sum(numbers: Vec<i32>, target: i32) -> Vec<i32> {
        let mut left = 0;
        let mut right = numbers.len() - 1;

        while left < right {
            let sum = numbers[left] + numbers[right];

            if sum == target {
                return vec![left as i32 + 1, right as i32 + 1];
            } else if sum < target {
                left += 1;
            } else {
                right -= 1;
            }
        }

        unreachable!() // 题目保证一定有解
    }
}
```
