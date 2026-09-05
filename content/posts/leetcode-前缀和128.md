---
title: "leetcode-前缀和128"
date: 2026-08-25T10:02:54+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 探索地牢的得分

给你一个 正整数 hp 和两个 正整数 数组 damage 和 requirement，数组下标从 1 开始。

有一个地牢，里面有 n 个陷阱房间，编号从 1 到 n。进入编号为 i 的房间会使你的生命值减少 damage[i]。减少后，如果你的剩余生命值至少为 requirement[i]，你可以从该房间获得 1 分。

定义 score(j) 为从房间 j 开始，依次进入房间 j, j + 1, ..., n 时可以获得的总分。

返回整数 score(1) + score(2) + ... + score(n)，即从所有起始房间计算的分数总和。

注意： 你不能跳过房间。即使你的生命值降为非正数，你仍然可以继续进入房间。


```
impl Solution {
    pub fn total_score(hp: i32, damage: Vec<i32>, requirement: Vec<i32>) -> i64 {
        let n = damage.len();
        // 前缀和：prefix[i] = damage[0..i) 之和
        let mut prefix = vec![0; n + 1];
        for i in 0..n {
            prefix[i + 1] = prefix[i] + damage[i];
        }

        let mut ans = 0i64;
        for i in 0..n {
            // 从房间 i 开始，在进入 i 之前生命值为 hp
            // 到达房间 i 时剩余生命值为 hp - prefix[i+1]（因为已经经过了 i+1 个房间）
            // 需要 hp - prefix[i+1] >= requirement[i] 才能得分
            // 即 hp - prefix[i+1] - requirement[i] >= 0
            // 但从 j 开始到 i 的总伤害为 prefix[i+1] - prefix[j]
            // 到达 i 时剩余 = hp - (prefix[i+1] - prefix[j])
            // 需要 hp - (prefix[i+1] - prefix[j]) >= requirement[i]
            // => prefix[j] >= prefix[i+1] + requirement[i] - hp
            let low = prefix[i + 1] + requirement[i] - hp;
            // 找到最小的 j 使得 prefix[j] >= low
            let j = prefix[0..=i].partition_point(|&v| v < low);
            // 从 j 到 i 都可以作为起始位置（共 i - j + 1 个）
            ans += (i - j + 1) as i64;
        }

        ans
    }
}
```
