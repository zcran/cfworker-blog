---
title: "leetcode-计数130"
date: 2026-08-01T10:40:58+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 最小展台数量

力扣嘉年华将举办一系列展览活动，后勤部将负责为每场展览提供所需要的展台。 已知后勤部得到了一份需求清单，记录了近期展览所需要的展台类型， demand[i][j] 表示第 i 天展览时第 j 个展台的类型。 在满足每一天展台需求的基础上，请返回后勤部需要准备的 最小 展台数量。

注意：

同一展台在不同天中可以重复使用。

```
impl Solution {
    /// 计算后勤部需要准备的最小展台数量。
    ///
    /// 同一展台可在不同天复用，因此每种展台类型的需求量取「所有天中的最大值」，
    /// 最终答案为各类展台最大需求量之和。
    pub fn min_num_booths(demand: Vec<String>) -> i32 {
        let mut max_need = [0i32; 26];

        for s in demand {
            // 栈上分配 26 个计数器，统计当天各类型展台需求
            let mut cnt = [0i32; 26];
            for b in s.bytes() {
                cnt[(b - b'a') as usize] += 1;
            }
            // 更新每种展台类型的历史最大需求
            for i in 0..26 {
                max_need[i] = max_need[i].max(cnt[i]);
            }
        }

        max_need.iter().sum()
    }
}
```
