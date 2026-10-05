---
title: "leetcode-二分查找96"
date: 2026-09-28T12:29:16+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 小张刷题计划

为了提高自己的代码能力，小张制定了 LeetCode 刷题计划，他选中了 LeetCode 题库中的 n 道题，编号从 0 到 n-1，并计划在 m 天内按照题目编号顺序刷完所有的题目（注意，小张不能用多天完成同一题）。

在小张刷题计划中，小张需要用 time[i] 的时间完成编号 i 的题目。此外，小张还可以使用场外求助功能，通过询问他的好朋友小杨题目的解法，可以省去该题的做题时间。为了防止“小张刷题计划”变成“小杨刷题计划”，小张每天最多使用一次求助。

我们定义 m 天中做题时间最多的一天耗时为 T（小杨完成的题目不计入做题总时间）。请你帮小张求出最小的 T是多少。


```
impl Solution {
    pub fn min_time(time: Vec<i32>, m: i32) -> i32 {
        let n = time.len();
        // 天数足够时，可以每天做一题并全部求助，耗时 0
        if n as i32 <= m {
            return 0;
        }

        // 判断在每天最多耗时 limit 的情况下，能否在 m 天内刷完
        let can_finish = |limit: i32| -> bool {
            let mut days = 1;
            let mut total = 0;      // 当天所有题目总耗时
            let mut max = 0;        // 当天最耗时的一题（可求助免做）
            let mut used_free = false; // 当天是否已使用求助

            for &t in &time {
                total += t;
                max = max.max(t);

                // 当天总耗时超出限制，尝试用求助免掉最耗时的一题
                while total > limit {
                    if used_free {
                        // 已用过求助，必须开启新的一天
                        days += 1;
                        if days > m {
                            return false;
                        }
                        used_free = false;
                        total = t;
                        max = t;
                    } else {
                        total -= max;
                        used_free = true;
                    }
                }
            }
            true
        };

        // 二分最小的每日最大耗时
        let mut left = 0;
        let mut right = time.iter().sum::<i32>();
        while left < right {
            let mid = left + (right - left) / 2;
            if can_finish(mid) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        left
    }
}
```
