---
title: "leetcode-回溯59"
date: 2026-07-04T10:22:04+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 拆分成最多数目的正偶数之和

给你一个整数 finalSum 。请你将它拆分成若干个 互不相同 的正偶数之和，且拆分出来的正偶数数目 最多 。

比方说，给你 finalSum = 12 ，那么这些拆分是 符合要求 的（互不相同的正偶数且和为 finalSum）：(2 + 10) ，(2 + 4 + 6) 和 (4 + 8) 。它们中，(2 + 4 + 6) 包含最多数目的整数。注意 finalSum 不能拆分成 (2 + 2 + 4 + 4) ，因为拆分出来的整数必须互不相同。

请你返回一个整数数组，表示将整数拆分成 最多 数目的正偶数数组。如果没有办法将 finalSum 进行拆分，请你返回一个 空 数组。你可以按 任意 顺序返回这些整数。


```
impl Solution {
    pub fn maximum_even_split(mut final_sum: i64) -> Vec<i64> {
        // 奇数无法拆分成偶数之和
        if final_sum % 2 == 1 {
            return Vec::new();
        }

        let mut ans = Vec::new();
        let mut current = 2;

        // 贪心：从小到大取最小的偶数，直到剩余值小于下一个偶数
        // 这样能保证拆分出最多数量的偶数
        while final_sum - current > current {
            ans.push(current);
            final_sum -= current;
            current += 2;
        }

        // 将剩余部分作为最后一个数
        ans.push(final_sum);
        ans
    }
}
```
