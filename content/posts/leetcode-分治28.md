---
title: "leetcode-分治28"
date: 2026-06-13T10:51:39+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 区间乘法查询后的异或 I


给你一个长度为 n 的整数数组 nums 和一个大小为 q 的二维整数数组 queries，其中 queries[i] = [li, ri, ki, vi]。

对于每个查询，按以下步骤执行操作：

· 设定 idx = li。
· 当 idx <= ri 时：
    · 更新：nums[idx] = (nums[idx] * vi) % (10^9 + 7)
    · 将 idx += ki。

在处理完所有查询后，返回数组 nums 中所有元素的 按位异或 结果。


```
impl Solution {
    /// 模数常量：用于数值乘法后的取模运算
    const MOD: i64 = 1_000_000_007;

    /// 执行区间乘法后计算异或结果
    ///
    /// # 参数
    /// - `nums`: 原始整数数组
    /// - `queries`: 查询数组，每个查询为 [l, r, k, v]
    ///   - 对下标满足 l ≤ i ≤ r 且 (i - l) % k == 0 的元素乘以 v
    ///
    /// # 返回
    /// 所有操作完成后，数组所有元素的异或值
    ///
    /// # 示例
    /// ```
    /// use solution::Solution;
    /// let nums = vec![1, 2, 3];
    /// let queries = vec![vec![0, 2, 1, 2]];
    /// assert_eq!(Solution::xor_after_queries(nums, queries), 0);
    /// ```
    pub fn xor_after_queries(nums: Vec<i32>, queries: Vec<Vec<i32>>) -> i32 {
        // 将 i32 转换为 i64 进行运算，避免溢出（最终结果会转回 i32）
        let mut nums = nums.iter().map(|&x| x as i64).collect::<Vec<_>>();

        // 依次处理每个查询
        for query in queries {
            // 解构查询参数
            let left = query[0] as usize;      // 区间左边界（包含）
            let right = query[1] as usize;     // 区间右边界（包含）
            let step = query[2] as usize;      // 步长：仅处理 (i - left) % step == 0 的位置
            let multiplier = query[3] as i64;  // 乘数因子

            // 遍历区间内所有步长位置
            let mut index = left;
            while index <= right {
                // 将当前元素乘以 multiplier，并对 MOD 取模
                nums[index] = (nums[index] * multiplier) % Self::MOD;
                index += step;  // 按步长跳转
            }
        }

        // 计算最终数组的异或值
        let mut result = 0;
        for value in nums {
            result ^= value;
        }

        result as i32  // 转回 i32 返回
    }
}
```
