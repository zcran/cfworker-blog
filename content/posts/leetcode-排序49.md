---
title: "leetcode-排序49"
date: 2026-10-05T00:35:02+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 将杂乱无章的数字排序

给你一个下标从 0 开始的整数数组 mapping ，它表示一个十进制数的映射规则，mapping[i] = j 表示这个规则下将数位 i 映射为数位 j 。

一个整数 映射后的值 为将原数字每一个数位 i （0 <= i <= 9）映射为 mapping[i] 。

另外给你一个整数数组 nums ，请你将数组 nums 中每个数按照它们映射后对应数字非递减顺序排序后返回。

注意：

如果两个数字映射后对应的数字大小相同，则将它们按照输入中的 相对顺序 排序。

nums 中的元素只有在排序的时候需要按照映射后的值进行比较，返回的值应该是输入的元素本身。



```
impl Solution {
    /// 将 nums 按映射后的值非递减排序，映射值相同时保持原相对顺序。
    ///
    /// 思路：
    /// 1. 定义映射函数，将数字的每一位按 mapping 转换后拼成新数字。
    /// 2. 对 nums 建立 (映射值, 原下标) 的辅助数组。
    /// 3. 按映射值升序排序，映射值相同时按下标升序，保证稳定排序效果。
    /// 4. 按排序结果取出原数字。
    pub fn sort_jumbled(mapping: Vec<i32>, nums: Vec<i32>) -> Vec<i32> {
        // 将单个数字按 mapping 转换
        let transfer = |mut x: i32| -> i32 {
            if x == 0 {
                return mapping[0];
            }
            let mut res = 0;
            let mut base = 1;
            while x > 0 {
                res += mapping[(x % 10) as usize] * base;
                base *= 10;
                x /= 10;
            }
            res
        };

        // (映射值, 原下标)，排序时同时保证稳定性
        let mut pairs: Vec<(i32, usize)> = nums
            .iter()
            .enumerate()
            .map(|(i, &v)| (transfer(v), i))
            .collect();

        pairs.sort_unstable_by_key(|&(mapped, idx)| (mapped, idx));

        pairs.into_iter().map(|(_, i)| nums[i]).collect()
    }
}
```
