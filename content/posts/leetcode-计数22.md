---
title: "leetcode-计数22"
date: 2026-08-01T10:40:51+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 通过投票对团队排名

现在有一个特殊的排名系统，依据参赛团队在投票人心中的次序进行排名，每个投票者都需要按从高到低的顺序对参与排名的所有团队进行排位。

排名规则如下：

参赛团队的排名次序依照其所获「排位第一」的票的多少决定。如果存在多个团队并列的情况，将继续考虑其「排位第二」的票的数量。以此类推，直到不再存在并列的情况。

如果在考虑完所有投票情况后仍然出现并列现象，则根据团队字母的字母顺序进行排名。

给你一个字符串数组 votes 代表全体投票者给出的排位情况，请你根据上述排名规则对所有参赛团队进行排名。

请你返回能表示按排名系统 排序后 的所有团队排名的字符串。


```
impl Solution {
    pub fn rank_teams(votes: Vec<String>) -> String {
        let num_teams = votes[0].len();

        // 使用二维数组统计每个团队在每个排名位置获得的票数
        // 索引：团队字母 (0-25)，维度：排名位置 (0..num_teams)
        let mut count = [[0; 26]; 26];

        // 统计每个投票者给出的排名
        for vote in &votes {
            for (pos, ch) in vote.chars().enumerate() {
                count[ch as usize - 'A' as usize][pos] += 1;
            }
        }

        // 获取所有参赛团队
        let mut teams: Vec<char> = votes[0].chars().collect();

        // 排序：先按排名统计，再按字母顺序
        teams.sort_unstable_by(|&a, &b| {
            let a_idx = a as usize - 'A' as usize;
            let b_idx = b as usize - 'A' as usize;

            // 比较每个排名位置的票数（从高到低）
            for pos in 0..num_teams {
                if count[a_idx][pos] != count[b_idx][pos] {
                    return count[b_idx][pos].cmp(&count[a_idx][pos]); // 降序
                }
            }

            // 所有排名票数相同，按字母升序
            a.cmp(&b)
        });

        teams.into_iter().collect()
    }
}
```
