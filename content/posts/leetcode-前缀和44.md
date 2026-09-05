---
title: "leetcode-前缀和44"
date: 2026-08-25T10:02:48+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 查询差绝对值的最小值

一个数组 a 的 差绝对值的最小值 定义为：0 <= i < j < a.length 且 a[i] != a[j] 的 |a[i] - a[j]| 的 最小值。如果 a 中所有元素都 相同 ，那么差绝对值的最小值为 -1 。

比方说，数组 [5,2,3,7,2] 差绝对值的最小值是 |2 - 3| = 1 。注意答案不为 0 ，因为 a[i] 和 a[j] 必须不相等。

给你一个整数数组 nums 和查询数组 queries ，其中 queries[i] = [li, ri] 。对于每个查询 i ，计算 子数组 nums[li...ri] 中 差绝对值的最小值 ，子数组 nums[li...ri] 包含 nums 数组（下标从 0 开始）中下标在 li 和 ri 之间的所有元素（包含 li 和 ri 在内）。

请你返回 ans 数组，其中 ans[i] 是第 i 个查询的答案。

子数组 是一个数组中连续的一段元素。

|x| 的值定义为：

如果 x >= 0 ，那么值为 x 。
如果 x < 0 ，那么值为 -x 。


```
impl Solution {
    /// 计算每个查询子数组中差绝对值的最小值
    ///
    /// 思路：
    /// 1. 预处理每个数字出现的位置（值域 1..100）
    /// 2. 对于每个查询，遍历值域，找到出现在区间内的所有数字
    /// 3. 计算相邻出现数字的最小差值
    pub fn min_difference(nums: Vec<i32>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        const MAX_VAL: usize = 100;

        // 记录每个数字出现的位置
        let mut positions = vec![Vec::new(); MAX_VAL + 1];
        for (i, &num) in nums.iter().enumerate() {
            positions[num as usize].push(i);
        }

        let mut ans = Vec::with_capacity(queries.len());

        for query in queries {
            let l = query[0] as usize;
            let r = query[1] as usize;

            let mut prev: Option<usize> = None;  // 上一个出现的数字
            let mut min_diff = i32::MAX;

            // 遍历值域，检查每个数字在区间内是否出现
            for val in 1..=MAX_VAL {
                let pos = &positions[val];
                // 二分查找第一个 >= l 的位置
                let idx = pos.binary_search(&l).unwrap_or_else(|i| i);

                // 如果该数字在区间内出现
                if idx < pos.len() && pos[idx] <= r {
                    if let Some(p) = prev {
                        let diff = (val - p) as i32;
                        min_diff = min_diff.min(diff);
                        if min_diff == 1 {
                            break; // 最小可能差值为 1，提前退出
                        }
                    }
                    prev = Some(val);
                }
            }

            ans.push(if min_diff == i32::MAX { -1 } else { min_diff });
        }

        ans
    }
}
```
