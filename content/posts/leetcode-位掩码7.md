---
title: "leetcode-位掩码7"
date: 2026-05-18T10:27:58+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 最小的必要团队


作为项目经理，你规划了一份需求的技能清单 req_skills，并打算从备选人员名单 people 中选出些人组成一个「必要团队」（ 编号为 i 的备选人员 people[i] 含有一份该备选人员掌握的技能列表）。

所谓「必要团队」，就是在这个团队中，对于所需求的技能列表 req_skills 中列出的每项技能，团队中至少有一名成员已经掌握。可以用每个人的编号来表示团队中的成员：

例如，团队 team = [0, 1, 3] 表示掌握技能分别为 people[0]，people[1]，和 people[3] 的备选人员。

请你返回 任一 规模最小的必要团队，团队成员用人员编号表示。你可以按 任意顺序 返回答案，题目数据保证答案存在。

```
impl Solution {
    pub fn smallest_sufficient_team(req_skills: Vec<String>, people: Vec<Vec<String>>) -> Vec<i32> {
        let n = req_skills.len();               // 技能总数（≤16）
        let m = people.len();                   // 候选人数

        // 技能 → 位索引
        let skill_index: std::collections::HashMap<_, _> = req_skills
            .into_iter()
            .enumerate()
            .map(|(i, s)| (s, i))
            .collect();

        // 每个人的技能掩码（使用 usize）
        let mut skill_mask = vec![0usize; m];
        for (i, skills) in people.iter().enumerate() {
            let mut mask = 0;
            for s in skills {
                mask |= 1 << skill_index[s];
            }
            skill_mask[i] = mask;
        }

        let full = (1 << n) - 1;
        // dp[mask] = (最小人数, 前一个mask, 最后加入的人)
        let mut dp = vec![(m + 1, 0usize, 0usize); 1 << n];
        dp[0] = (0, 0, 0);

        // 动态规划（0/1背包，倒序更新）
        for i in 0..m {
            let cur = skill_mask[i];
            if cur == 0 { continue; } // 此人无任何所需技能，跳过
            for prev in (0..=full).rev() {
                if dp[prev].0 == m + 1 { continue; } // 状态不可达
                let new_mask = prev | cur;
                let new_cnt = dp[prev].0 + 1;
                if new_cnt < dp[new_mask].0 {
                    dp[new_mask] = (new_cnt, prev, i);
                }
            }
        }

        // 回溯重建团队
        let mut result = Vec::new();
        let mut mask = full;
        while mask != 0 {
            let (_, prev_mask, person) = dp[mask];
            result.push(person as i32);
            mask = prev_mask;
        }
        result.reverse();
        result
    }
}
```
