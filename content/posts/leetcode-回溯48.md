---
title: "leetcode-回溯48"
date: 2026-07-04T10:22:04+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 最多可达成的换楼请求数目

我们有 n 栋楼，编号从 0 到 n - 1 。每栋楼有若干员工。由于现在是换楼的季节，部分员工想要换一栋楼居住。

给你一个数组 requests ，其中 requests[i] = [fromi, toi] ，表示一个员工请求从编号为 fromi 的楼搬到编号为 toi 的楼。

一开始 所有楼都是满的，所以从请求列表中选出的若干个请求是可行的需要满足 每栋楼员工净变化为 0 。意思是每栋楼 离开 的员工数目 等于 该楼 搬入 的员工数数目。比方说 n = 3 且两个员工要离开楼 0 ，一个员工要离开楼 1 ，一个员工要离开楼 2 ，如果该请求列表可行，应该要有两个员工搬入楼 0 ，一个员工搬入楼 1 ，一个员工搬入楼 2 。

请你从原请求列表中选出若干个请求，使得它们是一个可行的请求列表，并返回所有可行列表中最大请求数目。


```
impl Solution {
    pub fn maximum_requests(n: i32, requests: Vec<Vec<i32>>) -> i32 {
        let mut balance = vec![0; n as usize];
        let mut max_count = 0;
        Self::backtrack(&requests, 0, 0, &mut balance, &mut max_count);
        max_count as i32
    }

    /// 回溯搜索
    /// - requests: 所有请求列表
    /// - idx: 当前处理的请求索引
    /// - count: 已选择的请求数量
    /// - balance: 每栋楼的净变化（搬入-搬出）
    /// - max_count: 最大可行请求数
    fn backtrack(
        requests: &[Vec<i32>],
        idx: usize,
        count: usize,
        balance: &mut Vec<i32>,
        max_count: &mut usize,
    ) {
        // 剪枝：即使剩余所有请求都选择，也无法超过当前最优
        if count + (requests.len() - idx) <= *max_count {
            return;
        }

        // 所有请求处理完毕
        if idx == requests.len() {
            // 检查是否所有楼净变化为 0
            if balance.iter().all(|&x| x == 0) {
                *max_count = (*max_count).max(count);
            }
            return;
        }

        // 不选当前请求
        Self::backtrack(requests, idx + 1, count, balance, max_count);

        // 选择当前请求
        let from = requests[idx][0] as usize;
        let to = requests[idx][1] as usize;

        balance[from] -= 1;
        balance[to] += 1;

        Self::backtrack(requests, idx + 1, count + 1, balance, max_count);

        // 回溯
        balance[from] += 1;
        balance[to] -= 1;
    }
}
```
