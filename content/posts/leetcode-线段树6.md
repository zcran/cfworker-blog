---
title: "leetcode-线段树6"
date: 2026-06-19T09:37:24+08:00
tags: ["leetcode", "线段树"]
draft: false
---


## 找到按位或最接近 K 的子数组

给你一个数组 nums 和一个整数 k 。你需要找到 nums 的一个 子数组 ，满足子数组中所有元素按位或运算 OR 的值与 k 的 绝对差 尽可能 小 。换言之，你需要选择一个子数组 nums[l..r] 满足 |k - (nums[l] OR nums[l + 1] ... OR nums[r])| 最小。

请你返回 最小 的绝对差值。

子数组 是数组中连续的 非空 元素序列。

```
impl Solution {
    /// 寻找子数组 OR 值与 k 的最小绝对差
    ///
    /// 思路：固定右端点 i，维护所有可能的 OR 值及其对应的左端点集合。
    /// 随着 i 增加，OR 值最多有 31 种（因为每位只能从 0 变 1，不会回退）。
    /// 使用一个集合（实际用 Vec）存储当前所有不同的 OR 值及其最小左端点。
    ///
    /// 时间复杂度 O(n * 31)，空间复杂度 O(31)
    pub fn minimum_difference(nums: Vec<i32>, k: i32) -> i32 {
        const MOD: i32 = i32::MAX;
        let mut ans = MOD;
        // 存储 (or_value, min_left) 对，表示以当前右端点结尾的某些子数组的 OR 值及最小左端点
        let mut ors = Vec::new();

        for &num in &nums {
            // 新元素单独成为一个子数组
            let mut new_ors = vec![(num, 0)];

            // 将当前元素与之前的所有 OR 值组合，去重
            for &(or_val, _) in &ors {
                let new_or = or_val | num;
                // 如果 new_or 已经存在，保留最小的左端点（左端点越小越好，因为更灵活）
                if let Some(last) = new_ors.last_mut() {
                    if last.0 == new_or {
                        // 已经存在相同的 OR 值，无需重复添加
                        continue;
                    }
                }
                new_ors.push((new_or, 0));
            }

            // 更新为新的 OR 值集合
            ors = new_ors;

            // 计算当前所有 OR 值与 k 的差的绝对值
            for &(or_val, _) in &ors {
                ans = ans.min((or_val - k).abs());
            }
        }

        ans
    }
}
```
