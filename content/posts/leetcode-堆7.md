---
title: "leetcode-堆7"
date: 2026-08-21T09:54:11+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 相对名次

给你一个长度为 n 的整数数组 score ，其中 score[i] 是第 i 位运动员在比赛中的得分。所有得分都 互不相同 。

运动员将根据得分 决定名次 ，其中名次第 1 的运动员得分最高，名次第 2 的运动员得分第 2 高，依此类推。运动员的名次决定了他们的获奖情况：

名次第 1 的运动员获金牌 "Gold Medal" 。
名次第 2 的运动员获银牌 "Silver Medal" 。
名次第 3 的运动员获铜牌 "Bronze Medal" 。
从名次第 4 到第 n 的运动员，只能获得他们的名次编号（即，名次第 x 的运动员获得编号 "x"）。

使用长度为 n 的数组 answer 返回获奖，其中 answer[i] 是第 i 位运动员的获奖情况。


```
impl Solution {
    /// 将得分排名映射为奖牌/名次字符串
    pub fn find_relative_ranks(score: Vec<i32>) -> Vec<String> {
        let n = score.len();
        // 收集 (得分, 原索引)，按得分降序排列
        let mut order: Vec<(i32, usize)> = score.into_iter().enumerate().map(|(i, s)| (s, i)).collect();
        order.sort_unstable_by_key(|x| std::cmp::Reverse(x.0));

        // 预分配结果数组，按排名填充
        let mut ans = vec![String::new(); n];
        for (rank, (_, idx)) in order.into_iter().enumerate() {
            ans[idx] = match rank {
                0 => "Gold Medal".to_string(),
                1 => "Silver Medal".to_string(),
                2 => "Bronze Medal".to_string(),
                _ => (rank + 1).to_string(),
            };
        }
        ans
    }
}
```
