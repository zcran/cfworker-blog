---
title: "leetcode-贪心82"
date: 2026-10-03T12:55:39+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 你可以工作的最大周数

给你 n 个项目，编号从 0 到 n - 1 。同时给你一个整数数组 milestones ，其中每个 milestones[i] 表示第 i 个项目中的阶段任务数量。

你可以按下面两个规则参与项目中的工作：

每周，你将会完成 某一个 项目中的 恰好一个 阶段任务。你每周都 必须 工作。
在 连续的 两周中，你 不能 参与并完成同一个项目中的两个阶段任务。

一旦所有项目中的全部阶段任务都完成，或者执行仅剩的一个阶段任务将会导致你违反上面的规则，你将 停止工作。注意，由于这些条件的限制，你可能无法完成所有阶段任务。

返回在不违反上面规则的情况下你 最多 能工作多少周。


```
impl Solution {
    pub fn number_of_weeks(milestones: Vec<i32>) -> i64 {
        // 找出最大里程碑数及其总和
        let mut max = 0i64;
        let mut total = 0i64;
        for &m in &milestones {
            let m = m as i64;
            max = max.max(m);
            total += m;
        }

        // 其余里程碑总数
        let rest = total - max;
        // 若最大值超过其余总和 + 1，则只能交替完成 rest 个其他任务与 rest+1 个最长任务
        // 否则可以完成全部任务
        (rest * 2 + 1).min(total)
    }
}
```
