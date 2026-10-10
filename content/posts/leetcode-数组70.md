---
title: "leetcode-数组70"
date: 2026-10-10T20:38:45+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 电梯请求 I

给你一个整数 n ，表示一栋楼房的楼层数，楼层编号从 0 到 n - 1 。

同时给你一个整数数组 requests ，其中 requests 表示楼层请求的序列。

一部电梯初始在 0 层，遵循以下规则：

电梯每秒移动一层。
电梯按给定的顺序处理请求。
如果电梯已经在请求的楼层，则不需要移动。
处理完一个请求后，电梯立即开始向下一个请求的楼层移动。

返回处理所有请求所需的 总时间 （以秒为单位）。


```
impl Solution {
    pub fn elevator_requests(n: i32, requests: Vec<i32>) -> i32 {
        // 记录前一个请求楼层，初始在 0 层；累加每次移动的楼层差
        let mut pre = 0;
        let mut ans = 0;
        for req in requests {
            ans += (req - pre).abs();
            pre = req;
        }
        ans
    }
}
```
