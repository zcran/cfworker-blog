---
title: "leetcode-设计4"
date: 2026-07-14T10:41:24+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 区域和检索 - 数组不可变

给定一个整数数组  nums，处理以下类型的多个查询:

计算索引 left 和 right （包含 left 和 right）之间的 nums 元素的 和 ，其中 left <= right

实现 NumArray 类：

NumArray(int[] nums) 使用数组 nums 初始化对象

int sumRange(int left, int right) 返回数组 nums 中索引 left 和 right 之间的元素的 总和 ，包含 left 和 right 两点（也就是 nums[left] + nums[left + 1] + ... + nums[right] )


```
struct NumArray {
    /// 前缀和数组，prefix[i] 表示 nums[0..i) 的和
    /// 即 prefix[0] = 0, prefix[1] = nums[0], prefix[2] = nums[0] + nums[1], ...
    prefix: Vec<i32>,
}

impl NumArray {
    /// 使用数组 nums 初始化前缀和
    ///
    /// 时间复杂度: O(n)，空间复杂度: O(n)
    pub fn new(nums: Vec<i32>) -> Self {
        let mut prefix = Vec::with_capacity(nums.len() + 1);
        prefix.push(0); // prefix[0] = 0

        let mut sum = 0;
        for num in nums {
            sum += num;
            prefix.push(sum);
        }

        Self { prefix }
    }

    /// 计算索引 left 到 right（包含两端）的元素和
    ///
    /// 使用前缀和公式: sum(left..=right) = prefix[right + 1] - prefix[left]
    ///
    /// 时间复杂度: O(1)，空间复杂度: O(1)
    pub fn sum_range(&self, left: i32, right: i32) -> i32 {
        // 将参数转为 usize，注意题目保证 left <= right 且在有效范围内
        let l = left as usize;
        let r = right as usize;
        self.prefix[r + 1] - self.prefix[l]
    }
}
```
