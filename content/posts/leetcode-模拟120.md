---
title: "leetcode-模拟120"
date: 2026-08-08T11:31:16+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 转换数组

给你一个整数数组 nums，它表示一个循环数组。请你遵循以下规则创建一个大小 相同 的新数组 result ：

对于每个下标 i（其中 0 <= i < nums.length），独立执行以下操作：

如果 nums[i] > 0：从下标 i 开始，向 右 移动 nums[i] 步，在循环数组中落脚的下标对应的值赋给 result[i]。
如果 nums[i] < 0：从下标 i 开始，向 左 移动 abs(nums[i]) 步，在循环数组中落脚的下标对应的值赋给 result[i]。
如果 nums[i] == 0：将 nums[i] 的值赋给 result[i]。

返回新数组 result。

注意：由于 nums 是循环数组，向右移动超过最后一个元素时将回到开头，向左移动超过第一个元素时将回到末尾。


```
impl Solution {
    /// 根据循环数组规则构造变换后的新数组
    ///
    /// 规则：
    /// - nums[i] > 0：从 i 向右移动 nums[i] 步
    /// - nums[i] < 0：从 i 向左移动 |nums[i]| 步
    /// - nums[i] == 0：result[i] = 0
    ///
    /// 数组循环：右移越界回到开头，左移越界回到末尾
    ///
    /// # 参数
    /// - `nums`: 循环数组
    ///
    /// # 返回值
    /// - 变换后的新数组
    pub fn construct_transformed_array(nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len() as i32;

        nums.iter()
            .enumerate()
            .map(|(i, &offset)| {
                // 循环数组下标计算：先加偏移，再处理负数取模
                // (i + offset) % n 在 Rust 中若结果为负需要修正，
                // 通过 +n 后再 %n 确保结果在 [0, n-1] 范围内
                let target = ((i as i32 + offset) % n + n) % n;
                nums[target as usize]
            })
            .collect()
    }
}
```
