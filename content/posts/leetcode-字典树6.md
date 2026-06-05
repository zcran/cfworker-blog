---
title: "leetcode-字典树6"
date: 2026-06-02T10:23:07+08:00
tags: ["leetcode", "字典树"]
draft: false
---


## 数组中两个数的最大异或值

给你一个整数数组 nums ，返回 nums[i] XOR nums[j] 的最大运算结果，其中 0 ≤ i ≤ j < n 。


```
impl Solution {
    /// 寻找数组中两个数的最大异或值
    ///
    /// 异或运算的性质：a ^ b = c  =>  a = c ^ b
    ///
    /// # 参数
    /// - `nums`: 整数数组
    ///
    /// # 返回
    /// 数组中任意两个数的最大异或值
    ///
    /// # 示例
    /// ```
    /// let nums = vec![3, 10, 5, 25, 2, 8];
    /// assert_eq!(Solution::find_maximum_xor(nums), 28);
    /// // 解释: 5 ^ 25 = 28
    /// ```
    ///
    /// # 算法核心
    /// 贪心 + 哈希表（逐位确定最大异或值）
    ///
    /// 从最高位（30位）到最低位（0位），逐步构建答案：
    /// 1. 假设当前位可以为 1
    /// 2. 检查是否存在两个数，其前缀异或等于这个候选值
    /// 3. 如果存在，答案的这一位就是 1；否则为 0
    pub fn find_maximum_xor(nums: Vec<i32>) -> i32 {
        const MAX_BIT: i32 = 30;  // 数字最大为 10^9 < 2^30，所以最高位是 30

        // 从最高位向最低位构建答案
        // fold 初始值 x = 0，逐渐确定每一位
        (0..=MAX_BIT).rev().fold(0, |x, i| {
            // 步骤 1: 计算所有数字右移 i 位的值（前缀）
            // 例如：i=30 时，只保留最高位；i=29 时，保留最高两位
            let seen: std::collections::HashSet<_> = nums
                .iter()
                .map(|&num| num >> i)
                .collect();

            // 步骤 2: 假设当前位为 1，构造候选异或值
            // (x << 1) + 1 表示在已有前缀 x 后面添加 1
            // 例如：x=1 (二进制 01)，左移后是 10，加 1 得 11（即当前位为 1）
            let candidate = (x << 1) + 1;

            // 步骤 3: 检查是否存在两个数，其前缀异或等于 candidate
            // 即：是否存在 (a >> i) 和 (b >> i) 使得 a_prefix ^ b_prefix == candidate
            // 等价于：对任意 a_prefix，是否存在 b_prefix == candidate ^ a_prefix
            if nums.iter().any(|&num| seen.contains(&(candidate ^ (num >> i)))) {
                // 如果存在，当前位为 1
                candidate
            } else {
                // 如果不存在，当前位为 0
                x << 1
            }
        })
    }
}
```
