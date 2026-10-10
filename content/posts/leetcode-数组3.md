---
title: "leetcode-数组3"
date: 2026-10-10T20:38:40+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 汇总区间

给定一个  无重复元素 的 有序 整数数组 nums 。

区间 [a,b] 是从 a 到 b（包含）的所有整数的集合。

返回 恰好覆盖数组中所有数字 的 最小有序 区间范围列表 。也就是说，nums 的每个元素都恰好被某个区间范围所覆盖，并且不存在属于某个区间但不属于 nums 的数字 x 。

列表中的每个区间范围 [a,b] 应该按如下格式输出：

"a->b" ，如果 a != b
"a" ，如果 a == b


```
impl Solution {
    /// 汇总有序无重复数组中的连续区间。
    ///
    /// 用 `i` 标记区间起点，`j` 向后探测连续递增的边界。
    /// 若 `j - i == 1` 说明区间长度为 1，否则用 `a->b` 格式输出。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)（不计返回结果）。
    pub fn summary_ranges(nums: Vec<i32>) -> Vec<String> {
        let mut ans = Vec::new();
        let mut i = 0;

        while i < nums.len() {
            let start = nums[i];
            let mut j = i + 1;

            // 向后扫描连续递增的区间
            while j < nums.len() && nums[j] == nums[j - 1] + 1 {
                j += 1;
            }

            let end = nums[j - 1];
            ans.push(if start == end {
                start.to_string()
            } else {
                format!("{}->{}", start, end)
            });

            i = j;
        }

        ans
    }
}
```
