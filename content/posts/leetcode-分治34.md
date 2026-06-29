---
title: "leetcode-分治34"
date: 2026-06-13T10:51:39+08:00
tags: ["leetcode", "分治"]
draft: false
---

## 统计合格元素的数目

给你一个长度为 n 的整数数组 nums 和一个整数 k。

如果数组 nums 中的某个元素满足以下条件，则称其为 合格元素：存在 至少 k 个元素 严格大于 它。

返回一个整数，表示数组 nums 中合格元素的总数。


```
impl Solution {
    /// 计算数组中"合格元素"的数量
    ///
    /// 合格元素定义：存在至少 k 个元素严格大于它
    /// 即：该元素必须小于数组中第 k 大的元素
    ///
    /// # 参数
    /// - `nums`: 整数数组
    /// - `k`: 阈值，需要至少有 k 个元素严格大于当前元素
    ///
    /// # 返回
    /// 合格元素的总数
    ///
    /// # 示例
    /// ```
    /// use solution::Solution;
    /// let nums = vec![3, 1, 4, 1, 5, 9, 2, 6];
    /// assert_eq!(Solution::count_elements(nums, 2), 3);
    /// ```
    ///
    /// # 算法原理
    /// 1. 排序数组，第 k 大的元素位于索引 n-k 处
    /// 2. 所有小于第 k 大元素的元素都是合格元素
    /// 3. 使用二分查找定位第一个不小于第 k 大元素的位置
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n log n)，主要是排序开销
    /// - 空间复杂度：O(1)，原地排序
    pub fn count_elements(nums: Vec<i32>, k: i32) -> i32 {
        let n = nums.len();

        // 特殊情况：k = 0 时，所有元素都合格
        if k == 0 {
            return n as i32;
        }

        // 将 k 转换为 usize 用于索引
        let k_usize = k as usize;

        // 如果 k 大于等于数组长度，没有任何元素有 k 个元素大于它
        if k_usize >= n {
            return 0;
        }

        // 原地排序（不保留原数组顺序）
        let mut sorted = nums;
        sorted.sort_unstable(); // 使用不稳定排序，性能更好

        // 第 k 大的元素（从 0 开始索引）
        // 例如：n=5, k=2，第2大的元素在索引 5-2=3 处
        let kth_largest = sorted[n - k_usize];

        // 使用二分查找找到第一个 >= kth_largest 的位置
        // 这个位置之前的元素都严格小于 kth_largest
        let count = sorted.partition_point(|&x| x < kth_largest);

        count as i32
    }
}
```
