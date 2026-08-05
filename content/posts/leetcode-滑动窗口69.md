---
title: "leetcode-滑动窗口69"
date: 2026-07-18T11:02:33+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 数组的最大美丽值

给你一个下标从 0 开始的整数数组 nums 和一个 非负 整数 k 。

在一步操作中，你可以执行下述指令：

在范围 [0, nums.length - 1] 中选择一个 此前没有选过 的下标 i 。
将 nums[i] 替换为范围 [nums[i] - k, nums[i] + k] 内的任一整数。
数组的 美丽值 定义为数组中由相等元素组成的最长子序列的长度。

对数组 nums 执行上述操作任意次后，返回数组可能取得的 最大 美丽值。

注意：你 只 能对每个下标执行 一次 此操作。

数组的 子序列 定义是：经由原数组删除一些元素（也可能不删除）得到的一个新数组，且在此过程中剩余元素的顺序不发生改变。


```
impl Solution {
    pub fn maximum_beauty(nums: Vec<i32>, k: i32) -> i32 {
        let mut nums = nums;
        nums.sort_unstable();

        let mut max_len = 0;
        let mut left = 0;

        // 滑动窗口：维护窗口内所有元素可以变为同一个值
        // 条件：窗口内最大值 - 最小值 <= 2*k
        for right in 0..nums.len() {
            while nums[right] - nums[left] > 2 * k {
                left += 1;
            }
            max_len = max_len.max(right - left + 1);
        }

        max_len as i32
    }
}
```
