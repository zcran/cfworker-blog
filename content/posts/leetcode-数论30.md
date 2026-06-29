---
title: "leetcode-数论30"
date: 2026-06-20T11:09:56+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 分割数组使乘积互质

给你一个长度为 n 的整数数组 nums ，下标从 0 开始。

如果在下标 i 处 分割 数组，其中 0 <= i <= n - 2 ，使前 i + 1 个元素的乘积和剩余元素的乘积互质，则认为该分割 有效 。

例如，如果 nums = [2, 3, 3] ，那么在下标 i = 0 处的分割有效，因为 2 和 9 互质，而在下标 i = 1 处的分割无效，因为 6 和 3 不互质。在下标 i = 2 处的分割也无效，因为 i == n - 1 。
返回可以有效分割数组的最小下标 i ，如果不存在有效分割，则返回 -1 。

当且仅当 gcd(val1, val2) == 1 成立时，val1 和 val2 这两个值才是互质的，其中 gcd(val1, val2) 表示 val1 和 val2 的最大公约数。


```
impl Solution {
    /// 寻找最小分割下标，使得左右两部分乘积互质
    ///
    /// 核心思路：如果分割点左侧和右侧互质，意味着没有任何一个质因子同时出现在左右两侧
    /// 因此需要找到最后一个"重复质因子"出现的位置，分割点必须在其右侧
    pub fn find_valid_split(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        if n < 2 {
            return -1;
        }

        // 记录每个质因子最后出现的位置
        let mut last_pos = std::collections::HashMap::new();
        // right[i] 表示从 i 左侧必须扩展到的最大位置
        let mut right = vec![0; n];

        // 分解每个数并更新质因子的最后出现位置
        for (i, &num) in nums.iter().enumerate() {
            let mut x = num;
            let mut factor = 2;

            // 分解质因子
            while factor * factor <= x {
                if x % factor == 0 {
                    // 记录当前质因子的起始位置，并更新 right
                    let start = *last_pos.entry(factor).or_insert(i);
                    right[start] = right[start].max(i);

                    // 除去所有该质因子
                    while x % factor == 0 {
                        x /= factor;
                    }
                }
                factor += 1;
            }

            // 处理剩余的大质因子
            if x > 1 {
                let start = *last_pos.entry(x).or_insert(i);
                right[start] = right[start].max(i);
            }
        }

        // 寻找最小的分割点
        // 需要确保从 0 到 split 的所有元素中，质因子不再出现在 split 之后
        let mut farthest = 0;
        for i in 0..n - 1 {
            farthest = farthest.max(right[i]);
            // 如果当前位置 i 已经覆盖了所有重复质因子，可以分割
            if farthest == i {
                return i as i32;
            }
        }

        -1
    }
}
```
