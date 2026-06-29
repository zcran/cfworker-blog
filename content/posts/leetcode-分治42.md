---
title: "leetcode-分治42"
date: 2026-06-13T10:51:39+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 连续天数的最高销售额

某公司每日销售额记于整数数组 sales，请返回所有 连续 一或多天销售额总和的最大值。

要求实现时间复杂度为 O(n) 的算法。


```
impl Solution {
    /// 计算最大子数组和（Kadane算法）
    ///
    /// 时间复杂度: O(n)，空间复杂度: O(1)
    ///
    /// # 思路
    /// 动态维护以当前元素结尾的最大子数组和，
    /// 如果之前累计和为负，则从当前元素重新开始
    pub fn max_sales(nums: Vec<i32>) -> i32 {
        let mut max_sum = i32::MIN;   // 全局最大和
        let mut cur_sum = 0;          // 当前子数组和

        for &num in nums.iter() {
            // 累加当前元素，若cur_sum为负则从0开始（等价于max(num, cur_sum + num)）
            cur_sum = cur_sum.max(0) + num;
            max_sum = max_sum.max(cur_sum);
        }

        max_sum
    }
}
```
