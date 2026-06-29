---
title: "leetcode-分治33"
date: 2026-06-13T10:51:39+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 统计主要元素子数组数目 II

给你一个整数数组 nums 和一个整数 target。

返回数组 nums 中满足 target 是 主要元素 的 子数组 的数目。

一个子数组的 主要元素 是指该元素在该子数组中出现的次数 严格大于 其长度的 一半 。

子数组 是数组中的一段连续且 非空 的元素序列。


```
impl Solution {
    /// 计算数组中 target 作为主要元素的子数组数量
    ///
    /// 主要元素定义：在子数组中出现的次数严格大于其长度的一半
    ///
    /// # 算法原理
    /// 将 target 映射为 +1，非 target 映射为 -1
    /// 主要元素条件：count(target) > length/2
    /// 等价于：2*count(target) > length
    /// 等价于：count(target) - (length - count(target)) > 0
    /// 即：sum(映射值) > 0
    ///
    /// 使用前缀和 + 计数数组优化：
    /// - 用 s 表示当前前缀和（初始为 n，偏移 n 处理负数）
    /// - f 表示以当前元素结尾的满足条件的子数组数量
    /// - cnt 数组记录历史前缀和出现次数
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n)
    /// - 空间复杂度：O(n)
    ///
    /// # 示例
    /// ```
    /// use solution::Solution;
    /// let nums = vec![1, 2, 1, 1];
    /// assert_eq!(Solution::count_majority_subarrays(nums, 1), 6);
    /// ```
    pub fn count_majority_subarrays(nums: Vec<i32>, target: i32) -> i64 {
        let n = nums.len();

        // 计数数组：用于记录前缀和出现的次数
        // 前缀和范围：[-n, n]，偏移 n 后范围：[0, 2n]
        let mut count = vec![0; 2 * n + 1];
        let offset = n as isize; // 偏移量，将负数索引转换为非负数

        // 初始化：空前缀和为 0，出现 1 次
        count[offset as usize] = 1;

        let mut ans = 0i64;      // 最终答案
        let mut prefix = 0isize; // 当前前缀和（实际值，未偏移）
        let mut favorable = 0i64; // 以当前元素结尾的满足条件的子数组数量

        for &x in &nums {
            let idx = (prefix + offset) as usize;

            if x == target {
                // target 映射为 +1
                // 当前前缀和增加后，所有之前前缀和小于当前前缀和的都能形成主要元素子数组
                prefix += 1;
                let current_idx = (prefix + offset) as usize;
                // cnt[current_idx - 1] 表示所有小于当前前缀和的历史前缀和数量
                // 由于当前前缀和是递增的，实际需要统计所有 < current 的前缀和
                // 这里利用 cnt 数组通过累积方式维护
                favorable += count[current_idx - 1];
            } else {
                // 非 target 映射为 -1
                // 当前前缀和减少，需要减去那些不再满足条件的子数组
                // 即：之前以当前前缀和结尾的子数组不再满足 > 0 的条件
                prefix -= 1;
                let current_idx = (prefix + offset) as usize;
                favorable -= count[current_idx];
            }

            // 累加答案：所有以当前元素结尾的满足条件的子数组
            ans += favorable;

            // 记录当前前缀和的出现次数
            count[(prefix + offset) as usize] += 1;
        }

        ans
    }
}
```
