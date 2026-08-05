---
title: "leetcode-枚举55"
date: 2026-07-09T10:05:02+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 子集中元素的最大数量

给你一个 正整数 数组 nums 。

你需要从数组中选出一个满足下述条件的子集：

你可以将选中的元素放置在一个下标从 0 开始的数组中，并使其遵循以下模式：[x, x2, x4, ..., xk/2, xk, xk/2, ..., x4, x2, x]（注意，k 可以是任何 非负 的 2 的幂）。例如，[2, 4, 16, 4, 2] 和 [3, 9, 3] 都符合这一模式，而 [2, 4, 8, 4, 2] 则不符合。
返回满足这些条件的子集中，元素数量的 最大值 。


```
use std::collections::HashMap;

impl Solution {
    pub fn maximum_length(nums: Vec<i32>) -> i32 {
        // 统计每个数字出现的次数
        let mut count = HashMap::new();
        for &num in &nums {
            *count.entry(num as i64).or_insert(0) += 1;
        }

        // 处理数字 1：1 的任何次幂都是 1，可以形成任意长度的对称序列
        // 为了最大化，取最大奇数长度（必须中间有一个单独的 1）
        let ones = *count.get(&1).unwrap_or(&0);
        let mut ans = if ones % 2 == 1 { ones } else { ones - 1 };
        count.remove(&1);

        // 对于每个数字 x，尝试构建序列：x, x², x⁴, ..., 峰值, ..., x⁴, x², x
        // 需要每个中间数字至少出现 2 次，峰值出现至少 1 次
        for &x in count.keys() {
            let mut len = 0;
            let mut cur = x;

            // 向上构建：每个中间数字需要至少 2 个
            while let Some(&c) = count.get(&cur) {
                if c < 2 {
                    break;
                }
                len += 2; // 左右各一个

                // 下一个数字是当前数字的平方（可能溢出，但 i64 足够大）
                cur = cur * cur;
            }

            // 如果最后一个数字（峰值）存在至少 1 个，可以放在中间
            if count.contains_key(&cur) {
                len += 1;
            } else {
                // 否则峰值不存在，去掉最后一个成对的数字
                len -= 1;
            }

            ans = ans.max(len);
        }

        ans
    }
}
```
