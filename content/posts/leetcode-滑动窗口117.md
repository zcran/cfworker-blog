---
title: "leetcode-滑动窗口117"
date: 2026-07-18T11:02:36+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 美观的花束

力扣嘉年华的花店中从左至右摆放了一排鲜花，记录于整型一维矩阵 flowers 中每个数字表示该位置所种鲜花的品种编号。你可以选择一段区间的鲜花做成插花，且不能丢弃。 在你选择的插花中，如果每一品种的鲜花数量都不超过 cnt 朵，那么我们认为这束插花是 「美观的」。

例如：[5,5,5,6,6] 中品种为 5 的花有 3 朵， 品种为 6 的花有 2 朵，每一品种 的数量均不超过 3

请返回在这一排鲜花中，共有多少种可选择的区间，使得插花是「美观的」。

注意：

结果无需取模，用例保证输出为 int32 范围内的整数。


```
use std::collections::HashMap;

impl Solution {
    pub fn beautiful_bouquet(flowers: Vec<i32>, cnt: i32) -> i32 {
        let mut freq = HashMap::new();
        let mut left = 0;
        let mut ans = 0i32;

        for (right, &flower) in flowers.iter().enumerate() {
            // 1. 当前花朵加入窗口
            *freq.entry(flower).or_insert(0) += 1;

            // 2. 如果当前品种数量超过限制，收缩左边界
            while freq[&flower] > cnt {
                let out = flowers[left];
                let count = freq.get_mut(&out).unwrap();
                *count -= 1;
                if *count == 0 {
                    freq.remove(&out);
                }
                left += 1;
            }

            // 3. 所有以 right 为右端点的子数组都美观，共 right-left+1 个
            ans += (right - left + 1) as i32;
        }

        ans
    }
}
```
