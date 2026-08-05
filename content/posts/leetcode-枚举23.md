---
title: "leetcode-枚举23"
date: 2026-07-09T10:05:00+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 拿出最少数目的魔法豆

给定一个 正整数 数组 beans ，其中每个整数表示一个袋子里装的魔法豆的数目。

请你从每个袋子中 拿出 一些豆子（也可以 不拿出），使得剩下的 非空 袋子中（即 至少还有一颗 魔法豆的袋子）魔法豆的数目 相等。一旦把魔法豆从袋子中取出，你不能再将它放到任何袋子中。

请返回你需要拿出魔法豆的 最少数目。


```
impl Solution {
    /// 返回拿出魔法豆的最少数目。
    /// 策略：排序后，选择某个袋子作为最终保留的数量 x，
    /// 所有比 x 小的袋子清空，所有比 x 大的袋子减少到 x。
    /// 最优解中，x 一定是某个袋子的原始豆子数。
    pub fn minimum_removal(mut beans: Vec<i32>) -> i64 {
        // 升序排序
        beans.sort_unstable();

        let n = beans.len() as i64;
        let mut total_beans = 0i64;
        let mut max_keep = 0i64;

        // 枚举每个袋子作为保留数量的基准
        for (i, &count) in beans.iter().enumerate() {
            let count = count as i64;
            total_beans += count;

            // 如果以当前豆子数作为最终数量：
            // 可以保留的豆子 = 当前豆子数 * (当前及之后所有袋子数量)
            let keep = count * (n - i as i64);
            max_keep = max_keep.max(keep);
        }

        // 最少移除 = 总豆子数 - 最多可保留豆子数
        total_beans - max_keep
    }
}
```
