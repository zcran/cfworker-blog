---
title: "leetcode-二分查找62"
date: 2026-09-28T12:29:14+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 从一个范围内选择最多整数 I

给你一个整数数组 banned 和两个整数 n 和 maxSum 。你需要按照以下规则选择一些整数：

被选择整数的范围是 [1, n] 。
每个整数 至多 选择 一次 。
被选择整数不能在数组 banned 中。
被选择整数的和不超过 maxSum 。

请你返回按照上述规则 最多 可以选择的整数数目。


```
impl Solution {
    pub fn max_count(banned: Vec<i32>, n: i32, max_sum: i32) -> i32 {
        let n = n as usize;
        let mut banned_flag = vec![false; n + 1];
        for x in banned {
            if x as usize <= n {
                banned_flag[x as usize] = true;
            }
        }

        let mut ans = 0;
        let mut sum = 0_i64;
        for i in 1..=n {
            if banned_flag[i] {
                continue;
            }
            if sum + i as i64 > max_sum as i64 {
                break;
            }
            ans += 1;
            sum += i as i64;
        }
        ans
    }
}
```
