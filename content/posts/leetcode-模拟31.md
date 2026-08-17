---
title: "leetcode-模拟31"
date: 2026-08-08T11:31:10+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 按既定顺序创建目标数组

给你两个整数数组 nums 和 index。你需要按照以下规则创建目标数组：

· 目标数组 target 最初为空。
· 按从左到右的顺序依次读取 nums[i] 和 index[i]，在 target 数组中的下标 index[i] 处插入值 nums[i] 。
· 重复上一步，直到在 nums 和 index 中都没有要读取的元素。

请你返回目标数组。

题目保证数字插入位置总是存在。



```
impl Solution {
    /// 按 index 指定的位置依次插入 nums 的值
    /// 直接模拟插入操作即可
    pub fn create_target_array(nums: Vec<i32>, index: Vec<i32>) -> Vec<i32> {
        let mut target = Vec::with_capacity(nums.len());

        for (i, n) in index.into_iter().zip(nums) {
            target.insert(i as usize, n);
        }

        target
    }
}
```
