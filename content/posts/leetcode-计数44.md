---
title: "leetcode-计数44"
date: 2026-08-01T10:40:53+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 人口最多的年份

给你一个二维整数数组 logs ，其中每个 logs[i] = [birthi, deathi] 表示第 i 个人的出生和死亡年份。

年份 x 的 人口 定义为这一年期间活着的人的数目。第 i 个人被计入年份 x 的人口需要满足：x 在闭区间 [birthi, deathi - 1] 内。注意，人不应当计入他们死亡当年的人口中。

返回 人口最多 且 最早 的年份。


```
impl Solution {
    pub fn maximum_population(logs: Vec<Vec<i32>>) -> i32 {
        // 年份范围：1950 到 2050，共 101 年
        let mut delta = vec![0; 101];

        // 差分数组：出生年 +1，死亡年 -1（死亡当年不计入）
        for log in logs {
            let birth = (log[0] - 1950) as usize;
            let death = (log[1] - 1950) as usize;
            delta[birth] += 1;
            delta[death] -= 1;
        }

        // 前缀和计算每年人口，同时记录最大值和最早年份
        let mut max_pop = 0;
        let mut max_year = 1950;
        let mut cur_pop = 0;

        for (i, &d) in delta.iter().enumerate() {
            cur_pop += d;
            if cur_pop > max_pop {
                max_pop = cur_pop;
                max_year = 1950 + i as i32;
            }
        }

        max_year
    }
}
```
