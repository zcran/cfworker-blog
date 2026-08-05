---
title: "leetcode-枚举25"
date: 2026-07-09T10:05:00+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 花园的最大总美丽值

Alice 是 n 个花园的园丁，她想通过种花，最大化她所有花园的总美丽值。

给你一个下标从 0 开始大小为 n 的整数数组 flowers ，其中 flowers[i] 是第 i 个花园里已经种的花的数目。已经种了的花 不能 移走。同时给你 newFlowers ，表示 Alice 额外可以种花的 最大数目 。同时给你的还有整数 target ，full 和 partial 。

如果一个花园有 至少 target 朵花，那么这个花园称为 完善的 ，花园的 总美丽值 为以下分数之 和 ：

完善 花园数目乘以 full.
剩余 不完善 花园里，花的 最少数目 乘以 partial 。如果没有不完善花园，那么这一部分的值为 0 。

请你返回 Alice 种最多 newFlowers 朵花以后，能得到的 最大 总美丽值。


```
impl Solution {
    pub fn maximum_beauty(flowers: Vec<i32>, new_flowers: i64, target: i32, full: i32, partial: i32) -> i64 {
        let n = flowers.len();
        // 将花数截断至 target，超过的部分无用
        let mut flowers: Vec<i64> = flowers
            .into_iter()
            .map(|x| x.min(target) as i64)
            .collect();
        // 降序排列：前 i 个将作为“完善花园”候选
        flowers.sort_unstable_by(|a, b| b.cmp(a));

        let target = target as i64;
        let full = full as i64;
        let partial = partial as i64;

        // 总花数（截断后）
        let mut total: i64 = flowers.iter().sum();
        let mut ans = 0;

        // 提前判断：能否让所有花园都完善
        if target * n as i64 - total <= new_flowers {
            ans = full * n as i64;
        }

        // pre 表示前 i 个花园（最大的 i 个）当前已有的花总数
        let mut pre = 0;
        // ptr 指向“不完善部分”中，能通过补花统一提升的最小值的起始位置
        // 它只会向右移动，保证 O(n)
        let mut ptr = 0;

        // i 表示让前 i 个花园变得完善（i 从 0 到 n-1）
        for i in 0..n {
            if i > 0 {
                pre += flowers[i - 1];
            }
            // 若当前花园已经是 target，跳过（因为不可能把它作为不完善花园）
            if flowers[i] == target {
                continue;
            }

            // 使前 i 个花园全部达到 target 所需的花数
            let need_for_full = target * i as i64 - pre;
            let mut rest = new_flowers - need_for_full;
            if rest < 0 {
                break; // 后续 i 增大，需要更多花，提前结束
            }

            // 移动 ptr，使得从 ptr 到 n-1 这部分花园可以均匀提升
            // 条件：将 flowers[ptr..n) 全部提升到 flowers[ptr] 所需的花 ≤ rest
            while !(ptr >= i && (flowers[ptr] * (n - ptr) as i64 - total) <= rest) {
                total -= flowers[ptr];
                ptr += 1;
            }

            // 此时，剩余的花 rest 可以用于提升 flowers[ptr..n) 的最小值
            let extra = rest - (flowers[ptr] * (n - ptr) as i64 - total);
            // 可以提升到的最大最小值（不能超过 target-1，因为不完善）
            let min_flowers = (flowers[ptr] + extra / (n - ptr) as i64).min(target - 1);

            let beauty = full * i as i64 + partial * min_flowers;
            ans = ans.max(beauty);
        }

        ans
    }
}
```
