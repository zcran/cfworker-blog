---
title: "leetcode-排序82"
date: 2026-10-05T00:35:05+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 电梯请求 II

给你一个整数 n 表示一栋建筑的楼层数，楼层编号从 0 到 n - 1 。

同时给你一个整数 start ，表示电梯的起始楼层，以及一个整数数组 requests ，其中 requests[i] 是电梯需要到达的楼层。requests 中的所有楼层都是 互不相同 的。

在时间 0 ，电梯在楼层 start ，所有请求都是 同时 发出的。

在所有请求被处理完之前的每一秒钟，电梯 恰好 移动一层，可以是 向上 也可以是 向下 。当电梯到达请求的楼层时，该请求会被 立即 处理。如果 start 出现在 requests 中，则该请求在时间 0 被处理。

对于每个未被处理的请求，每一秒钟你会受到 1 点惩罚。等价地说，在时间 t 处理一个请求，它对总惩罚的贡献是 t 。

返回处理所有请求所需的 最小 总惩罚。


```
impl Solution {
    pub fn elevator_requests(n: i32, start: i32, mut requests: Vec<i32>) -> i64 {
        // 加入 start 和两个哨兵，排序后便于区间 DP
        requests.push(start);
        requests.push(-1);
        requests.push(n);
        requests.sort_unstable();

        let m = requests.len();
        const INF: i64 = i64::MAX / 2;

        // memo[i][j][is_right]：
        //   已处理 requests[i..=j] 之间的所有请求，
        //   is_right = false 表示电梯停在 requests[i]，
        //   is_right = true  表示电梯停在 requests[j]。
        let mut memo = vec![vec![[-1i64; 2]; m]; m];

        // 递归 + 记忆化搜索
        fn dfs(
            i: usize,
            j: usize,
            is_right: usize,
            m: usize,
            requests: &Vec<i32>,
            memo: &mut Vec<Vec<[i64; 2]>>,
        ) -> i64 {
            // 越界（碰到哨兵）
            if i == 0 || j == m - 1 {
                return INF;
            }
            // 所有请求处理完毕
            if i == 1 && j == m - 2 {
                return 0;
            }
            if memo[i][j][is_right] != -1 {
                return memo[i][j][is_right];
            }

            let x = requests[if is_right == 1 { j } else { i }];
            // 剩余未处理的请求数量（用于计算惩罚增量）
            let remain = (m - 3 - j + i) as i64;

            // 向左移动到 requests[i-1]
            let go_left = dfs(i - 1, j, 0, m, requests, memo)
                + (x - requests[i - 1]) as i64 * remain;
            // 向右移动到 requests[j+1]
            let go_right = dfs(i, j + 1, 1, m, requests, memo)
                + (requests[j + 1] - x) as i64 * remain;

            memo[i][j][is_right] = go_left.min(go_right);
            memo[i][j][is_right]
        }

        // 找到 start 在排序后数组中的位置
        let start_idx = requests.binary_search(&start).unwrap();
        dfs(start_idx, start_idx, 0, m, &requests, &mut memo)
    }
}
```
