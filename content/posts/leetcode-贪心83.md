---
title: "leetcode-贪心83"
date: 2026-10-03T12:55:39+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 构造元素不等于两相邻元素平均值的数组

给你一个 下标从 0 开始 的数组 nums ，数组由若干 互不相同的 整数组成。你打算重新排列数组中的元素以满足：重排后，数组中的每个元素都 不等于 其两侧相邻元素的 平均值 。

更公式化的说法是，重新排列的数组应当满足这一属性：对于范围 1 <= i < nums.length - 1 中的每个 i ，(nums[i-1] + nums[i+1]) / 2 不等于 nums[i] 均成立 。

返回满足题意的任一重排结果。


```
impl Solution {
    pub fn rearrange_array(nums: Vec<i32>) -> Vec<i32> {
        let mut nums = nums;
        nums.sort_unstable();

        // 排序后，从下标 1 开始每隔一个元素与从末尾对应位置交换，
        // 使奇数下标位置为较大的一半，偶数下标位置为较小的一半，
        // 从而任意相邻三元素不构成等差（中间值不等于两侧平均值）
        let n = nums.len();
        let mut i = 1;
        let mut j = n - 2 + (n & 1);
        while i < j {
            nums.swap(i, j);
            i += 2;
            j -= 2;
        }
        nums
    }
}
```
