---
title: "leetcode-单调队列19"
date: 2026-03-31T20:55:29+08:00
tags: ["leetcode", "单调队列"]
draft: false
---


## 最小分割分数

给你一个整数数组 nums 和一个整数 k。

你的任务是将 nums 分割成 恰好 k 个子数组，并返回所有有效分割方案中 最小可能的分数。

一个分割方案的 分数 是其所有子数组 值 的 总和。

子数组的 值 定义为 sumArr * (sumArr + 1) / 2，其中 sumArr 是该子数组元素的总和。

子数组 是数组中连续的非空元素序列。

```
struct Point {
    x: i64,
    y: i64,
}

impl Point {
    pub fn sub(&self, b: &Point) -> Self {
        Self::new(self.x - b.x, self.y - b.y)
    }
    pub fn det(&self, b: &Point) -> i64 {
        self.x * b.y - self.y * b.x
    }
    pub fn dot(&self, b: &Point) -> i64 {
        self.x * b.x + self.y * b.y
    }
    pub fn new(x: i64, y: i64) -> Self {
        Self { x, y }
    }
}

/**
 * Tag: 划分DP + 斜率优化(凸包优化)
 */

impl Solution {
    pub fn min_partition_score(nums: Vec<i32>, k: i32) -> i64 {
        let n = nums.len();
        let k = k as usize;
        let mut acc = vec![0i64; n + 1];
        for i in 0..n {
            acc[i + 1] = acc[i] + nums[i] as i64;
        }

        let inf = 1i64 << 55;
        let mut dp = vec![inf; n + 1];
        dp[0] = 0;

        for i in 1..=k {
            let mut pre = acc[i - 1];
            let mut q = std::collections::VecDeque::new();
            q.push_back(Point::new(pre, dp[i - 1] + pre * pre - pre));

            for j in i..=(n + i - k) {
                pre = acc[j];
                let p = Point::new(-2 * pre, 1);
                let v = Point::new(pre, dp[j] + pre * pre - pre);
                // 单调队列优化
                while q.len() > 1 {
                    let (fi, se) = (q.get(0).unwrap(), q.get(1).unwrap());
                    if p.dot(fi) < p.dot(se) {
                        break;
                    }
                    q.pop_front();
                }

                let fi = q.get(0).unwrap();
                dp[j] = p.dot(fi) + pre * pre + pre;

                // 找凸包点
                while q.len() > 1 {
                    let (back_fi, back_se) =
                        (q.get(q.len() - 1).unwrap(), q.get(q.len() - 2).unwrap());
                    if back_fi.sub(back_se).det(&v.sub(back_fi)) > 0 {
                        break;
                    }
                    q.pop_back();
                }

                q.push_back(v);
            }
        }
        dp[n] / 2
    }
}

```
