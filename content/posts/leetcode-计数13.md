---
title: "leetcode-计数13"
date: 2026-08-01T10:40:51+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 总持续时间可被 60 整除的歌曲

在歌曲列表中，第 i 首歌曲的持续时间为 time[i] 秒。

返回其总持续时间（以秒为单位）可被 60 整除的歌曲对的数量。形式上，我们希望下标数字 i 和 j 满足  i < j 且有 (time[i] + time[j]) % 60 == 0。


```
impl Solution {
    pub fn num_pairs_divisible_by60(time: Vec<i32>) -> i64 {
        // 统计每个余数出现的次数（0~59）
        let mut count = [0; 60];
        for &t in &time {
            count[(t % 60) as usize] += 1;
        }

        // 组合数 C(n, 2) = n * (n - 1) / 2
        let comb2 = |n: i64| n * (n - 1) / 2;

        let mut pairs = 0;

        // 余数为 0 的情况：任意两个余数 0 的歌曲配对
        pairs += comb2(count[0]);

        // 余数为 30 的情况：任意两个余数 30 的歌曲配对
        pairs += comb2(count[30]);

        // 余数互补配对：余数 i 和 60-i 配对（i = 1..29）
        for i in 1..30 {
            pairs += count[i] * count[60 - i];
        }

        pairs
    }
}
```
