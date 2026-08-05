---
title: "leetcode-设计10"
date: 2026-07-14T10:41:25+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 在线选举

给你两个整数数组 persons 和 times 。在选举中，第 i 张票是在时刻为 times[i] 时投给候选人 persons[i] 的。

对于发生在时刻 t 的每个查询，需要找出在 t 时刻在选举中领先的候选人的编号。

在 t 时刻投出的选票也将被计入我们的查询之中。在平局的情况下，最近获得投票的候选人将会获胜。

实现 TopVotedCandidate 类：

TopVotedCandidate(int[] persons, int[] times) 使用 persons 和 times 数组初始化对象。

int q(int t) 根据前面描述的规则，返回在时刻 t 在选举中领先的候选人的编号。


```
use std::collections::HashMap;

/// 在给定时刻查询领先候选人的数据结构
struct TopVotedCandidate {
    /// 每个时刻的领先候选人，与 times 一一对应
    leading: Vec<i32>,
    /// 投票时刻数组
    times: Vec<i32>,
}

impl TopVotedCandidate {
    /// 使用投票记录初始化
    ///
    /// 预处理：计算每个时刻的领先候选人
    /// 时间复杂度: O(n)，空间复杂度: O(n)
    pub fn new(persons: Vec<i32>, times: Vec<i32>) -> Self {
        let mut vote_count = HashMap::new();
        let mut current_leader = 0;
        let mut leading = Vec::with_capacity(persons.len());

        for person in persons {
            // 先获取当前领先者的票数（不可变借用）
            let leader_votes = *vote_count.get(&current_leader).unwrap_or(&0);

            // 再更新当前候选人的票数（可变借用）
            let count = vote_count.entry(person).or_insert(0);
            *count += 1;

            // 如果当前候选人票数 >= 当前领先者票数，则成为新领先者
            // 使用 >= 确保平局时最近投票的人获胜
            if *count >= leader_votes {
                current_leader = person;
            }

            leading.push(current_leader);
        }

        Self { leading, times }
    }

    /// 查询时刻 t 的领先候选人
    ///
    /// 使用二分查找定位 t 在 times 中的位置
    /// 时间复杂度: O(log n)，空间复杂度: O(1)
    pub fn q(&self, t: i32) -> i32 {
        // 二分查找第一个 > t 的时刻
        let (mut left, mut right) = (0, self.times.len());
        while left < right {
            let mid = (left + right) / 2;
            if self.times[mid] > t {
                right = mid;
            } else {
                left = mid + 1;
            }
        }

        // left 是第一个 > t 的索引，因此 left - 1 是 <= t 的最大索引
        self.leading[left - 1]
    }
}
```
