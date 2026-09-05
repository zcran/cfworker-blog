---
title: "leetcode-堆60"
date: 2026-08-21T09:54:14+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 移山所需的最少秒数

给你一个整数 mountainHeight 表示山的高度。

同时给你一个整数数组 workerTimes，表示工人们的工作时间（单位：秒）。

工人们需要 同时 进行工作以 降低 山的高度。对于工人 i :

山的高度降低 1，需要 workerTimes[i] 秒。
山的高度降低 2，需要 workerTimes[i] * 2 秒。
...
山的高度降低 x，需要 workerTimes[i] * x 秒。

工人 i 所花费的总时间是所有 x 单位所需时间的总和。由于所有工人同时操作，所需的总时间是任何工人花费的 最大 时间。

返回一个整数，表示工人们使山的高度降低到 0 所需的 最少 秒数。


```
impl Solution {
    pub fn min_number_of_seconds(mountain_height: i32, worker_times: Vec<i32>) -> i64 {
        let h = mountain_height as i64;

        // 二分答案：最少需要多少秒
        let mut l = 1i64;
        let mut r = *worker_times.iter().max().unwrap_or(&1) as i64 * h * (h + 1) / 2;
        let mut ans = r;

        while l <= r {
            let mid = l + (r - l) / 2;
            let mut total = 0i64;

            for &t in &worker_times {
                let work = mid / t as i64; // 工人 i 能完成的"工作量单位"
                // 求最大 k 满足 k*(k+1)/2 <= work，即工人能削多少层
                let mut kl = 0i64;
                let mut kr = (2 * work).min(h); // k 不会超过 h 或 2*work
                while kl <= kr {
                    let km = kl + (kr - kl) / 2;
                    if km * (km + 1) / 2 <= work {
                        kl = km + 1;
                    } else {
                        kr = km - 1;
                    }
                }
                total += kr;
                if total >= h { break; } // 提前退出
            }

            if total >= h {
                ans = mid;
                r = mid - 1;
            } else {
                l = mid + 1;
            }
        }

        ans
    }
}
```
