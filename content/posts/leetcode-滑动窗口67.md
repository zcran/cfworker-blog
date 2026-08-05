---
title: "leetcode-滑动窗口67"
date: 2026-07-18T11:02:33+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 统计没有收到请求的服务器数目

给你一个整数 n ，表示服务器的总数目，再给你一个下标从 0 开始的 二维 整数数组 logs ，其中 logs[i] = [server_id, time] 表示 id 为 server_id 的服务器在 time 时收到了一个请求。

同时给你一个整数 x 和一个下标从 0 开始的整数数组 queries  。

请你返回一个长度等于 queries.length 的数组 arr ，其中 arr[i] 表示在时间区间 [queries[i] - x, queries[i]] 内没有收到请求的服务器数目。

注意时间区间是个闭区间。


```
impl Solution {
    pub fn count_servers(n: i32, logs: Vec<Vec<i32>>, x: i32, queries: Vec<i32>) -> Vec<i32> {
        let n = n as usize;
        let nq = queries.len();

        // 对查询按时间排序，保留原始索引
        let mut query_idx: Vec<usize> = (0..nq).collect();
        query_idx.sort_by_key(|&i| queries[i]);

        // 对日志按时间排序
        let mut logs = logs;
        logs.sort_by_key(|log| log[1]);

        let mut ans = vec![0; nq];
        let mut cnt = vec![0; n + 1];  // 记录每个服务器当前在窗口内的请求数
        let mut out_of_range = n as i32;  // 窗口外服务器数量
        let mut left = 0;
        let mut right = 0;
        let m = logs.len();

        for &idx in &query_idx {
            let query_time = queries[idx];
            let window_start = query_time - x;

            // 扩大右边界：将时间 <= query_time 的日志加入窗口
            while right < m && logs[right][1] <= query_time {
                let server = logs[right][0] as usize;
                if cnt[server] == 0 {
                    out_of_range -= 1;  // 该服务器首次进入窗口
                }
                cnt[server] += 1;
                right += 1;
            }

            // 缩小左边界：将时间 < window_start 的日志移出窗口
            while left < m && logs[left][1] < window_start {
                let server = logs[left][0] as usize;
                cnt[server] -= 1;
                if cnt[server] == 0 {
                    out_of_range += 1;  // 该服务器完全离开窗口
                }
                left += 1;
            }

            ans[idx] = out_of_range;
        }

        ans
    }
}
```
