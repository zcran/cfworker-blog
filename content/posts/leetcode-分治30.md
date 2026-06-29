---
title: "leetcode-分治30"
date: 2026-06-13T10:51:39+08:00
tags: ["leetcode", "分治"]
draft: false
---



## 最长平衡子数组 I

给你一个整数数组 nums。

如果子数组中 不同偶数 的数量等于 不同奇数 的数量，则称该 子数组 是 平衡的 。

返回 最长 平衡子数组的长度。

子数组 是数组中连续且 非空 的一段元素序列。

```
use std::collections::HashMap;

impl Solution {
    /// 寻找最长的"平衡"子数组
    ///
    /// 平衡定义：子数组中奇数和偶数的不同元素个数相等
    /// 即：子数组中出现奇数次的数字种类数 = 出现偶数次的数字种类数
    ///
    /// # 参数
    /// - `nums`: 整数数组
    ///
    /// # 返回
    /// 最长的平衡子数组长度
    ///
    /// # 示例
    /// ```
    /// use solution::Solution;
    /// assert_eq!(Solution::longest_balanced(vec![1, 2, 3, 4]), 2);
    /// ```
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n²)，n 为数组长度
    /// - 空间复杂度：O(n)，用于存储频数统计
    pub fn longest_balanced(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let mut max_len = 0;

        // 枚举所有可能的子数组起点
        for start in 0..n {
            // 统计当前子数组中奇数和偶数数字的出现次数
            // key: 数字值, value: 出现次数
            let mut odd_freq = HashMap::new();    // 奇数频数表
            let mut even_freq = HashMap::new();   // 偶数频数表

            // 扩展子数组终点
            for end in start..n {
                let current = nums[end];

                // 根据奇偶性更新对应的频数表
                if current & 1 == 1 {
                    *odd_freq.entry(current).or_insert(0) += 1;
                } else {
                    *even_freq.entry(current).or_insert(0) += 1;
                }

                // 检查是否平衡：奇数和偶数的不同数字数量相等
                if odd_freq.len() == even_freq.len() {
                    let length = (end - start + 1) as i32;
                    max_len = max_len.max(length);
                }
            }
        }

        max_len
    }
}
```
